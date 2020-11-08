<template>
	<el-button
		:size="size"
		:type="type"
		:plain="plain"
		:round="round"
		:circle="circle"
		:icon="icon"
		:loading="loading"
		:disabled="disabled"
		@click="toExport"
	>
		<slot>导出</slot>
	</el-button>
</template>

<script>
import { export_json_to_excel } from "../utils/export2excel";
import { isArray, isFunction, isEmpty, currentDate } from "../utils";

export default {
	props: {
		filename: [Function, String],
		autoWidth: {
			type: Boolean,
			default: true,
		},
		bookType: {
			type: String,
			default: "xlsx",
		},
		header: Array,
		fields: Array,
		data: [Function, Array],
		maxExportLimit: Number, // 最大导出条数，不传或者小于等于0为不限制
		size: {
			type: String,
			default: "mini",
		},
		disabled: Boolean,
		type: String,
		plain: Boolean,
		round: Boolean,
		circle: Boolean,
		icon: String,
	},

	data() {
		return {
			loading: false,
		};
	},

	methods: {
		async toExport() {
			if (!this.$crud) {
				return console.error("未获取到 $crud。请注入或者刷新页面");
			}

			// 加载
			this.loading = true;

			// cl-crud loaded
			const { app, ctx } = this.$crud;

			// 表格列
			const columns = app
				.getData("table.columns")
				.filter((e) => !["selection", "expand", "index"].includes(e.type));

			// 字段
			const fields = isEmpty(this.fields)
				? columns.filter((e) => !(e.filterExport || e["filter-export"])).map((e) => e.prop)
				: this.fields;

			// 表头
			let header = await this.getHeader(columns, fields);

			// 数据
			let data = await this.getData();

			if (!data) {
				this.loading = false;
				return console.error("导出数据异常");
			}

			// 文件名
			let filename = await this.getFileName();

			// 过滤
			data = data.map((d) => fields.map((f) => d[f]));

			// 导出 excel
			export_json_to_excel({
				header,
				data,
				filename,
				autoWidth: this.autoWidth,
				bookType: this.bookType,
			});

			this.loading = false;
		},

		async getHeader(columns, fields) {
			return (
				this.header || columns.filter((e) => fields.includes(e.prop)).map((e) => e.label)
			);
		},

		async getData() {
			if (isFunction(this.data)) {
				return await this.data();
			} else {
				if (this.data) {
					return this.data;
				} else {
					const { getData, paramsReplace } = this.$crud.app;
					const params = paramsReplace(getData("params"));

					return getData("service")
						.page({
							...params,
							maxExportLimit: this.maxExportLimit,
							isExport: true,
						})
						.then((res) => res.list)
						.catch((err) => {
							console.error(err);
							return null;
						});
				}
			}
		},

		async getFileName() {
			if (isFunction(this.filename)) {
				return await this.filename();
			} else {
				const { year, month, day, hour, minu, sec } = currentDate();
				return this.filename || `报表（${year}-${month}-${day} ${hour}_${minu}_${sec}）`;
			}
		},
	},
};
</script>
