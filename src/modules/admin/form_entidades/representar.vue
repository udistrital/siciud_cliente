<template>
	<div class="col mt-3 pl-1 pr-1" id="paneltrabajosP">
		<div class="row">
			<div class="col">
				<div class="p-0">
					<div class="page-header header-elements-md-inline mb-2">
						<div class="page-title p-0 m-0">
							<h1>
								<i class="icon-grid3 mr-1 color-main-600"></i>
								<span class="font-weight-semibold">{{ title }}</span>
								<span class="item-title">&nbsp;</span>
							</h1>
						</div>
						<div class="header-elements">
							<span class="cmds">
								<button type="button" @click.prevent="add()" v-if="editMode" title="Nuevo Elemento.." class="btn btn-main btn-labeled btn-labeled-left ">
									<b><i class="icon-database-add"></i></b> Nuevo Representante
								</button>
							</span>
							<span class="cmds-back slide">
								<button type="button" @click.prevent="retorno()" title="Volver al trabajo.." class="btn btn-main btn-labeled btn-labeled-left ">
									<b><i class="icon-arrow-left"></i></b> Volver A {{ title }}
								</button>
							</span>
						</div>
					</div>
				</div>
			</div>
		</div>
		<!-- <Documentos id="paneltrabajosP-documentos" end-point="hist_legal_representatives" :main-obj="baseObj" :parent="this" :tipos="tiposDocumento" /> -->
		<!-- <Participantes id="paneltrabajosP-participantes" end-point="hist_legal_representatives" :product="baseObj" :group="group" ref="participantes" :parent="this" /> -->
		<DxValidationGroup ref="basicGroup">
			<div class="row data slide">
				<div class="col">
					<div class="card">
						<div class="card-header main">
							<i class="icon-pencil3 mr-1"></i>
							<span class="font-weight-semibold">{{ mode == "edit" ? "Editar" : "Crear" }} Representante</span>
						</div>
						<div class="card-body mb-0 pb-0 pt-2">
							<div class="row">
								<!-- formulatio -->
<div class="col-md-3">
	<div class="form-group">
		<label>Documento de identidad:</label>
		<DxNumberBox
		class="form-control"
		:show-clear-button="true"
		:value.sync="baseObj.identification_number"
		placeholder="Documento de identidad"
		>
		<DxValidator>
			<DxRequiredRule />
		</DxValidator>
		<DxNumberBoxButton :options="searchButton" name="search" location="after" />
		</DxNumberBox>
	</div>
</div>

<div class="col-md-2">
	<div class="form-group">
	<label>Tipo Documento: </label>
	<DxSelectBox
		:show-clear-button="true"
		:grouped="false"
		:search-enabled="false"
		placeholder="Seleccione..."
		:value.sync="baseObj.identification_type_id" 
		class="form-control"
		:data-source="tipoDocumento" 
		display-expr="st_name"
		value-expr="id">
		<DxValidator>
		<DxRequiredRule />
	</DxValidator>
	</DxSelectBox>
	</div>
</div>



<div class="col-md-3">
	<div class="form-group">
	<label>Nombre Representante: </label>
	<DxTextBox placeholder="Nombre Representante" class="form-control" :value.sync="baseObj.name">
	<DxValidator>
		<DxRequiredRule />
	</DxValidator>
	</DxTextBox>
	</div>
</div> 

<div class="col-md-3">
	<div class="form-group">
	<label>Email: </label>
	<DxTextBox placeholder="Email" class="form-control" :value.sync="baseObj.email">
	<DxValidator>
		<DxRequiredRule message="Es importante digitar correo de la entidad"/>
        <DxEmailRule message="El correo es invalido"/>
		<DxRequiredRule />
	</DxValidator>
	</DxTextBox>
	</div>
</div>

<div class='col-md-1'>
	<div class='form-group'>
	<label>Actual: </label>
	<DxSwitch :value.sync='baseObjhist.is_current' switched-on-text='SI' switched-off-text='NO' />
	</div>
</div>

								<!-- fin formulario -->
							</div>
						</div>
						<div class="card-footer">
							<div class="row">
								<div class="col">
									<DxButton @click="cancel" class="nb">
										<template #default>
											<span class="btn btn-main btn-labeled btn-labeled-left btn-sm legitRipple">
												<b><i class="icon-database-remove"></i></b> CANCELAR
											</span>
										</template>
									</DxButton>
								</div>
								<div class="col text-right">
									<DxButton @click="save" class="nb" v-if="editMode">
										<template #default>
											<span class="btn btn-main btn-labeled btn-labeled-right btn-sm legitRipple">
												GUARDAR <b><i class="icon-database-add"></i></b>
											</span>
										</template>
									</DxButton>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</DxValidationGroup>
		<div class="row grid">
			<div class="col">
				<div class="p-0">
					<DxDataGrid
						class="main"
						width="100%"
						@initialized="gridInit"
						@content-ready="onContentReady"
						:allow-column-reordering="true"
						no-data-text="No hay elementos registrados"
						:data-source="dataSource"
						:remote-operations="true"
						:hover-state-enabled="true"
						:row-alternation-enabled="true"
						:show-borders="false"
					>
						<DxColumnChooser :enabled="totaCount > 0" mode="dragAndDrop" />
						<DxSorting mode="single" /><!-- single, multiple, none" -->
						<DxPaging :page-size="10" />
						<DxFilterRow :visible="false" />
						<DxLoadPanel :enabled="false" />
						<DxGroupPanel :visible="totaCount > 0" :allow-column-dragging="true" />
						<DxGrouping :auto-expand-all="false" />
						<DxSummary>
							<DxGroupItem summary-type="count" column="group_type_name" display-format="{0} elementos" />
						</DxSummary>
						<DxPager
							:show-info="true"
							:show-page-size-selector="true"
							:show-navigation-buttons="true"
							:allowed-page-sizes="dgPageSizes"
							info-text="Página {0} de {1} ({2} elementos)"
						/>
						<DxSearchPanel :visible="false" :highlight-case-sensitive="true" />
						<!-- https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxDataGrid/Configuration/columns/ -->

						<DxColumn data-field="id" caption="ID" data-type="text" alignment="center" :visible="true" :allow-grouping="false" :width="100"/>
						<DxColumn data-field="legal_representative_name" caption="Nombre Completo" data-type="text" alignment="center" :visible="true" :allow-grouping="false" />
						<DxColumn data-field="legal_representative_identification" caption="Documento" data-type="text" alignment="center" :visible="true" :allow-grouping="false" />
						<DxColumn data-field="legal_representative_email" caption="Email" data-type="text" alignment="center" :visible="true" :allow-grouping="false" />

						<DxColumn data-field="is_current" caption="Actual" data-type="date" alignment="center" :visible="true" :customize-text="yesNo" width="100" />
						<DxColumn data-field="active" caption="Activo" data-type="date" alignment="center" :visible="true" :customize-text="yesNo" width="70" />
						<DxColumn :width="70" alignment="center" cell-template="tpl" caption="" />

						<template #tplObs="{ data }">
							<a
								v-if="data.data.dw_observation != '' && data.data.dw_observation != null"
								:title="data.data.dw_observation"
								class="cmd-item color-main-600 mr-2"
								@click.prevent="verObservar(data.data)"
								href="#"
								Target="_blank"
							>
								<i class="icon-info mr-1"></i> Ver
							</a>
							<a v-else title="No dispone" class="cmd-item color-main-600 mr-2" href="#">-</a>
						</template>

						<template #tpl="{ data }">
							<span class="cmds">
								<a title="Editar elemento..." class="cmd-item color-main-600" @click.prevent="edit(data.data)" href="#">
									<i class="icon-database-edit"></i>
								</a>
								<a v-if="data.data.active" title="Desactivar Trabajos..." class="cmd-item color-main-600 mr-2" @click.prevent="active(data, false)" href="#">
									<i class="icon-database-remove"></i>
								</a>
								<a v-else title="Activar Trabajos..." class="cmd-item color-main-600 mr-2" @click.prevent="active(data, true)" href="#">
									<i class="icon-database-check"></i>
								</a>
							</span>
						</template>
					</DxDataGrid>
				</div>
			</div>
		</div>

		<div class="card mt-3" v-if="debug">
			<div class="card-body">
				{{ JSON.stringify(baseObj, null, "\t") }}
			</div>
			<div class="card-body">
				{{ JSON.stringify(baseObjhist, null, "\t") }}
			</div>
		</div>
		<DxPopup :visible="popupObs" :drag-enabled="false" :close-on-outside-click="false" :show-title="true" width="60%" height="300" title="Observación:">
			<div class="row" style="overflow-y: scroll; height:148px">
				<div class="col">
					<h3>
						<i class="icon-info mr-1 color-main-600"></i>
						<span class="font-weight-semibold">{{ baseObj[titlecolum] }}</span>
					</h3>
					<div v-html="observarData"></div>
				</div>
			</div>
			<div class="row">
				<div class="col">
					<hr />
					<DxButton @click="popupObs = false" class="nb">
						<template #default>
							<span class="btn btn-main btn-labeled btn-labeled-left btn-sm legitRipple">
								<b><i class="icon-database-remove"></i></b> Salir
							</span>
						</template>
					</DxButton>
				</div>
			</div>
		</DxPopup>
	</div>
</template>

<script>
/* eslint-disable no-unused-vars */
/* eslint-disable vue/no-unused-components */
let root = null;
let $ = window.jQuery;
import DxStore from "@/store/dx";
import {
	DxColumn,
	DxColumnChooser,
	DxDataGrid,
	DxFilterRow,
	DxGroupItem,
	DxGroupPanel,
	DxGrouping,
	DxLoadPanel,
	DxPager,
	DxPaging,
	DxSearchPanel,
	DxSorting,
	DxSummary,
} from "devextreme-vue/data-grid";
import { DxButton as DxNumberBoxButton } from "devextreme-vue/number-box";
import { DxEmailRule, DxRequiredRule, DxStringLengthRule, DxValidator, DxPatternRule } from "devextreme-vue/validator";
import { DxDateBox, DxSelectBox, DxButton, DxTagBox, DxTextBox, DxNumberBox, DxTextArea, DxValidationGroup, DxPopup, DxSwitch } from "devextreme-vue";
import { mapState, mapActions, mapGetters } from "vuex";

// https://js.devexpress.com/Demos/WidgetsGallery/Demo/DataGrid/CustomDataSource/Vue/
export default {
	name: "Representantes",
	components: {
		// Commands,
		DxNumberBoxButton,
		DxPopup,
		DxButton,
		DxColumn,
		DxPatternRule,
		DxColumnChooser,
		DxDataGrid,
		DxDateBox,
		DxNumberBox,
		DxFilterRow,
		DxSwitch,
		DxGrouping,
		DxGroupItem,
		DxGroupPanel,
		DxLoadPanel,
		DxPager,
		DxPaging,
		DxRequiredRule,
		DxSearchPanel,
		DxSelectBox,
		DxSorting,
		DxSummary,
		DxTextArea,
		DxTextBox,
		DxValidator,
		DxValidationGroup,
		DxEmailRule, DxStringLengthRule,
		// Geo: () => import("@/components/element/geo"),
		// Observaciones: () => import("@/components/element/html_editor"),
		// Documentos: () => import("@/components/element/documentos"),
		// Participantes: () => import("@/components/element/participantes"),
	},
	props: {
		titlecolum: {
			type: String,
			default: () => "dw_title",
		},
		group: {
			type: Object,
			default: () => null,
		},
		title: {
			type: String,
			default: null,
		},
		id_trabajo: {
			type: String,
			default: null,
		},
	},
	data: () => ({
		popupObs: false,
		observarData: "",
		editData: null, //sirve para dejar formulario en limpio o llenar datos
		items: [],
		totaCount: 0,
		grid: null,
		mode: null,
		modeRep: null,
		unidad: null,
		section: null,
		tipos: 166,
		totalCount: 0,
		tipoDocumento: [],
		subtipos: [],
		isValid: false,
		panelData: null,
		panelGrid: null,
		panelParticipantes: null,
		panelDocs: null,
		panelCmds: null,
		baseEntity: null,
		docLink: null,
		firstLoad: true,
		now: new Date(),
		min: new Date(1950, 1, 1),
		baseEnt: null,
		urlPattern: /^(http|https):\/\/[^ "]+$/,
		phonePattern: /^\+\s*1\s*\(\s*[02-9]\d{2}\)\s*\d{3}\s*-\s*\d{4}$/,
		baseObj: {
			id:null,
			name: null, 
			identification_number: null,
			email: null, 
			identification_type_id: null, 
			active: true,
		},
		baseObjhist:{
			legal_representative_id: null,
			is_current: true,
			active: true,
		},
		datosDoc:{},
		searchButton: {
			icon: "find",
			// text: "Buscar",
			onClick: async () => {
				let doc=root.baseObj.identification_number;
				console.clear();
				console.warn("Boton buscar tocado", doc);
				root.datosDoc = await root.getSerarchDoc({url: 'legal_representatives?identification_number=' + doc});
				root.baseObj = root.baseEnt;
				root.baseObj.identification_number=doc;
				if(root.datosDoc.length > 0){
					root.baseObj=root.datosDoc[0];
					root.modeRep = "edit";
					root.baseObjhist.legal_representative_id=root.baseObj.id;
				}else{
					root.$info("Complete los datos del Formulario para anexar nuevo representante legal");
					root.modeRep = "add";
				}
			},
		},
		ruta:null,
	}),
	created() {
		root = this;
		root.baseEnt = this.$clone(this.baseObj);
		// root.getConvocatorias();
		//root.tipos = root.subtypesByType("articulo_tipo");
		// root.subtipos = root.subtypesByType("trabajo_grado_p_categoria");
		// root.tiposDocumento = root.subtypesByType("trabajo_grado_p_documento");
		root.tipoDocumento = root.subtypesByType("entidades_documento");
	},
	mounted() {
		// root.getConvocatorias();
		// root.tipos = root.subtypesByType(5);
		// root.subtipos = root.subtypesByType(32);
		console.log("root.tipos", root.tipos);
		root.panelData = $("#paneltrabajosP .data");
		root.panelGrid = $("#paneltrabajosP .grid");
		root.panelCmds = $("#paneltrabajosP .cmds");
		root.panelCmdBack = $("#paneltrabajosP .cmds-back");
		root.panelDocs = $("#paneltrabajosP-documentos");
		root.loaderMessage = "Cargando elementos";
		root.loaderElement = "#paneltrabajosP .grid";
	},
	computed: {
		...mapGetters("core/tipo", ["subtypesByType"]),
		...mapState("unidad/colciencias", { convocatorias: "items" }),
		dataSource: function() {
			if (typeof this.$route.params.idEnt === "undefined") return null;
			console.log("root.group", this.group);
			return DxStore({
				key: ["id"],
				// ids: ["dw_type_id=1"],
				stringParam: "dw_type_id=" + root.tipos,
				endPoint: `entities/${root.$route.params.idEnt}/hist_legal_representatives/`,
				onLoading: function(loadOptions) {
					root.loaderShow("Cargando elementos", "#panel-produccion .card-body");
				},
				onLoaded: function(results, baseEntity) {
					// console.clear();
					console.log("results", results);
					root.totaCount = results.totalCount;
					root.loaderHide();
				},
			});
		},
		editComp: async () =>{
			let doc=root.baseObj.legal_representative_identification;
			root.datosDoc= await root.getSerarchDoc({url: 'legal_representatives?identification_number=' + doc});
			root.baseObj.identification_number=doc;
			root.baseObj=root.datosDoc[0];
			root.modeRep = "edit";
		}
	},
	watch: {},
	methods: {
		// ...mapActions("unidad/colciencias", { getConvocatorias: "getAll" }),
		//...mapActions("unidad/producto/conocimiento/articulo", { objSave: "save", objUpdate: "update", elementoActive: "active" }),
		...mapActions("unidad/producto/universalSentUpAct", { objSave: "save", objUpdate: "update", elementoActive: "active", getSerarchDoc: "univerdalGet"}),

		saveRepresentante() {
			// if (result.isValid) {
				// let msg = (root.mode == "add" ? "Creando" : "Actualizando") + " elemento";
				if (root.modeRep == "add") root.baseObj.created_by = root.user_id;
				if (root.modeRep == "edit") root.baseObj.updated_by = root.user_id;
				let obj = root.baseObj;
				let dto = {
					newFormat: true,
					// unidadId: root.$route.params.idEnt,
					stringEP: "legal_representatives",
					rute2: "legal_representatives",
					mod: obj.id,
					objectSend: { legal_representative: obj },
					cb: function(item) {
						console.log("item", item);
						root.baseObjhist.legal_representative_id=item.id;
						// root.grid.refresh();
						// root.loaderHide();
						// root.cancel();
					},
				};
				if (root.modeRep == "edit") root.objUpdate(dto);
				else root.objSave(dto);
				// root.cancel();
			// }
		},

		save() {
			console.log(this.$sep);
			var result = root.$refs.basicGroup.instance.validate();
			root.baseObj.dw_observation = root.baseObj.observation;
			console.log("result", result);
			if (result.isValid) {
				root.saveRepresentante();
				console.log("VALID!");
				root.scrollTop();
				root.panelCmds.fadeOut();
				let msg = (root.mode == "add" ? "Creando" : "Actualizando") + " elemento";
				root.loaderShow(msg, root.panelData);
				if (root.mode == "add") root.baseObjhist.created_by = root.user_id;
				if (root.mode == "edit") root.baseObjhist.updated_by = root.user_id;
				
				// root.baseObjhist.is_current=true;
				let obj = root.baseObjhist;
				let dto = {
					newFormat: true,
					unidadId: root.$route.params.idEnt,
					stringEP: root.ruta,
					rute2: root.ruta,
					mod: obj.id,
					objectSend: { hist_legal_representative: obj },
					cb: function(item) {
						console.log("item", item);
						root.grid.refresh();
						root.loaderHide();
						root.cancel();
					},
				};
				console.log("root.mode", root.mode);
				if (root.mode == "edit") root.objUpdate(dto);
				else root.objSave(dto);
				root.cancel();
			}
		},

		edit(data) {
			root.mode = "edit";
			console.log("data", data);
			root.baseObj = data;
			root.baseObjhist.id=data.id;
			root.editComp;
			root.ruta="hist_legal_representatives"
			// root.baseObj.observation = root.baseObj.dw_observation;
			root.panelCmds.fadeOut();
			root.panelGrid.fadeOut(function(params) {
				root.panelData.fadeIn(function(params) {});
			});
			
		},

		add() {
			console.log("ADD");
			root.mode = "add";
			root.ruta="entities/"+root.$route.params.idEnt+"/hist_legal_representatives"
			root.baseObj = this.$clone(this.baseEnt);
			root.panelCmds.fadeOut();
			root.panelGrid.fadeOut(function(params) {
				root.panelData.fadeIn(function(params) {});
			});
		},

		cancel() {
			console.log("CANCEL!");
			root.panelData.fadeOut(function(params) {
				root.panelCmds.fadeIn();
				root.panelGrid.fadeIn(function(params) {});
			});
		},

		active(data, state) {
			// console.clear();
			console.log("active", data);
			console.log("state", state);
			let a = state ? "activar" : "desactivar";
			let am = state ? "Activando" : "Desactivando";
			let msg = `¿Realmente desea ${a} <span class='text-sb'>"${data.data.legal_representative_name}"</span>?`;
			this.$confirm(msg, function(si_no) {
				console.log("result", si_no);
				if (si_no) {
					root.loaderShow(`${am}`, root.panelGrid);
					var dto = {
						url: `hist_legal_representatives/${data.data.id}`,
						data: {
							hist_legal_representative: {
								active: state,
								updated_by: 1,
							},
						},
						cb: function(result) {
							console.log("Result", result);
							root.grid.refresh();
							root.loaderHide();
							// root.cancel(validationGroup);
							// $("#data").fadeOut(function () {
							// $("#grid").fadeIn(function () {});
							// });
						},
					};
					console.log("dto", dto);
					root.elementoActive(dto);
					root.loaderHide();
				}
			});
		},

		gridInit(e) {
			this.grid = e.component;
		},

		onContentReady() {
			// $(".commands a").click(function() {
			// 	console.log("Come on lets show the dropdown!!");
			// });
			// var h = "<span class='mr-1 color-text d-none d-md-inline' id='column-chooser-text'>s</span> ";
			// if ($("#column-chooser-text").length <= 0) $(".dx-datagrid-column-chooser-button").before(h);
			// var b = "<span class='mr-1 color-text d-none d-md-inline' id='column-chooser-text'>s</span> ";
			// if ($("#column-chooser-text").length <= 0) $(".dx-datagrid-column-chooser-button").before(b);
		},
	},
};
</script>
