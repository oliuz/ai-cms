<template>
	<div :class="dataCol">
		<div class="form-material form-material-primary" :class="{'floating':isFloating}">
			<select :class="dataClass" :data-tags="dataMultiple" :data-placeholder="valPlaceholder" :name="dataName" :required="isRequired" :multiple="dataMultiple">
				<option v-if="!dataMultiple"></option>
				<option v-for="item in sourceData" :value="item.value">{{ item.key }}</option>
			</select>
			<label>{{ dataLabel }}</label>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				currentVal: null,
				items: []
			};
		},
		props: {
			dataCol: { type: Array, required: false, default() { return ['col-md-6']; }},
			dataClass: { type: Array, required: false, default() { return ['form-control', 'js-select2']; }},
			dataCustomSource: { type: Array, required: false, default() { return []; }},
			dataLabel: { type: String, required: false, default: ''},
			dataName: { type: String, required: true},
			dataMultiple: { type: Boolean, required: false, default: false },
			dataMethod: { type: String, required: false, default: 'get' },
			dataOptions: { type: Object, required: false, default() { return {}; }},
			dataPlaceholder: { type: String, required: false, default: ''},
			dataSource: { type: String, required: false, default: 'custom' },
			dataValue: { type: String, required: false, default: ''},
			dataValues: { type: Array, required: false, default() { return []; }},
			isRequired: { type: Boolean, required: false, default: false},
			isFloating: { type: Boolean, required: false, default: false}
		},
		computed: {
			valPlaceholder() {
				if (this.isFloating) {
					return '';
				} else {
					return this.dataPlaceholder;
				}
			},
			sourceData() {
				if (this.dataSource == 'custom') {
					return this.dataCustomSource;
				} else {
					return this.items;
				}
			}
		},
		methods: {
			clear() {
				$("select[name='" + this.dataName + "']").val("");
				$("select[name='" + this.dataName + "']").trigger('change');
			},
			refreshSelect() {
				var that = this;

				this.$nextTick(function () {
					$("select[name='" + that.dataName + "']").select2();
					$("select[name='" + that.dataName + "']").val(that.currentVal).trigger('change');
					$("select[name='" + that.dataName + "']").on('select2:select', that.valueChanged);
					$("select[name='" + that.dataName + "']").on('select2:unselect', that.valueChanged);
				});
			},
			valueChanged(evt) {
				this.currentVal = $("select[name='" + this.dataName + "']").val();
			},
			setValue() {
				if (this.dataMultiple) {
					this.currentVal = this.dataValues;
				} else {
					this.currentVal = this.dataValue;
				}

				this.refreshSelect();
			},
			errorResponse(response) {
				this.setValue();
			},
			successResponse(response) {
				this.items = [];
				this.items = response.data;

				this.setValue();
			},
			loadData(url) {
				//Load Data From url;
				var that = this;

				if (this.dataSource == 'custom') return;

				switch (this.dataMethod.toUpperCase()) {
					case 'GET':
						this.$http.get(this.dataSource, {params: that.dataOptions}).then(that.successResponse, that.errorResponse);
					break;
					case 'POST':
						this.$http.post(this.dataSource, {body: that.dataOptions}).then(that.successResponse, that.errorResponse);
					break;
				}
			}
		},
		created() {
			bus.$on('input-clear', this.clear);
		},
		mounted() {
			this.loadData();
		}
	}
</script>
