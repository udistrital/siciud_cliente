// Advertencia: // para la ejeccuon de estas vistas se requiere instalar: // npm install jspdf jspdf-autotable

<template>
	<div>
		<div class="page-header header-elements-md-inline">
			<div class="page-title d-flex">
				<h1>
					<i class="icon-books mr-1 color-main-600"></i>
					<span class="font-weight-semibold" id="title"> Plan de Acción e Informe de Gestión</span>
					<span> &raquo; Historial </span>
				</h1>
				<a href="#" class="header-elements-toggle text-default d-md-none"><i class="icon-more"></i></a>
			</div>

			<div class="header-elements" v-if="true">
				<a
					:href="rutaprincipal"
					title="Volver a Unidades..."
					class="
            btn btn-sm btn-main btn-labeled btn-labeled-left
            legitRipple
            ml-2
          "
				>
					<b><i class="icon-arrow-left2"></i></b> Estructuras
				</a>

				<!-- <a
          href="#"
          v-if="editMode"
          @click.prevent="excelDoc2()"
          title="Exportar a Excel..."
          class="
            btn btn-sm btn-main btn-labeled btn-labeled-left
            legitRipple
            ml-2
          "
        >
          <b><i class="icon-database-add"></i></b>Exportar Excel
        </a> -->
				<!-- </div> -->
			</div>
		</div>

		<div class="row">
			<div class="col">
				<div class="card" id="panel-plan-accion">
					<div class="card-body group-detail mh">
						<div class="row mb-3">
							<div class="col">
								<div class="col d-flex justify-content-between align-items-end">
									<div class="title">
										<i class="icon-history"></i><small> <i>Plan de Acción e Informe de Gestión</i> &raquo; Histórico</small>
									</div>
								</div>
							</div>
						</div>

						<p></p>
						<div class="row">
							<div class="col-md-12">
								<div class="row grid">
									<div class="col">
										<h2></h2>
										<div class="p-0">
											<DxDataGrid
												class="main"
												width="100%"
												@exporting="excelDoc"
												key-expr="ID"
												:data-source="dataSource"
												:remote-operations="true"
												id="gridContainer"
												:show-borders="false"
											>
												<!--
				<DxExport icon="save" :enabled="true" :allow-export-selected-data="false" />
				-->

												<!-- type="custom" :custom-load="loadState" :custom-save="saveState" -->
												<DxColumnChooser :enabled="true" mode="dragAndDrop" />

												<!-- <DxFilterPanel :visible="true" /> -->
												<DxFilterRow :visible="true" />
												<DxGrouping :auto-expand-all="true" />
												<DxGroupPanel :visible="true" />
												<DxLoadPanel :enabled="false" />
												<DxPaging :page-size="10" />
												<DxSorting :mode="'multiple'" /><!-- single, multiple, none" -- > -->
												<!-- <DxStateStoring :enabled="true" type="sessionStorage" /> -->
												<DxSummary>
													<DxGroupItem summary-type="count" column="group_type_name" display-format="{0} Planes o Informes" />
												</DxSummary>
												<DxPager
													:show-info="true"
													:show-page-size-selector="true"
													:show-navigation-buttons="true"
													:allowed-page-sizes="dgPageSizes"
													info-text="{2} Planes e Informes de Gestión(Página {0} de {1})"
												/>
												<DxSearchPanel :visible="false" :highlight-case-sensitive="true" />

												<!-- https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxDataGrid/Configuration/columns/ -->

												<DxColumn
													:allow-filtering="false"
													:sort-index="1"
													sort-order="desc"
													data-field="id"
													caption="ID"
													data-type="number"
													alignment="center"
													:allow-sorting="true"
													:width="70"
												/>
												<!-- https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxDataGrid/Configuration/columns/ -->

												<!-- <DxColumn data-field="id" :width="80"  caption="ID" data-type="string" alignment="center" :visible="true" :allow-grouping="false" /> -->

												<DxColumn
													:allow-filtering="true"
													:allow-grouping="false"
													:allow-sorting="true"
													data-field="research_group_name"
													caption="Grupo"
													data-type="string"
													alignment="left"
													:visible="true"
												/>
												<DxColumn
													:allow-filtering="true"
													:allow-grouping="false"
													:allow-sorting="true"
													data-field="research_group_acronym"
													width="90"
													caption="Acrónimo"
													data-type="string"
													alignment="left"
													:visible="true"
												/>
												<DxColumn
													:allow-filtering="true"
													:allow-grouping="false"
													:allow-sorting="true"
													data-field="execution_validity"
													width="90"
													caption="Vigencia"
													data-type="number"
													alignment="center"
													:visible="true"
												/>

												<DxColumn
													data-field="created_at"
													width="90"
													caption="Creación"
													data-type="date"
													alignment="center"
													:visible="true"
													:allow-filtering="true"
													:allow-grouping="false"
													:allow-sorting="true"
												/>
												<DxColumn
													data-field="updated_at"
													width="90"
													caption="Actualización"
													data-type="date"
													alignment="center"
													:visible="true"
													:allow-filtering="true"
													:allow-grouping="false"
													:allow-sorting="true"
												/>

												<!-- <DxColumn data-field='observation'  caption='Observaciones' data-type='string' alignment='center' :visible='true'  cell-template="tplObs"/>  -->
												<DxColumn
													data-field="is_draft"
													caption="Plan Acción"
													data-type="string"
													alignment="center"
													:visible="true"
													:allow-filtering="true"
													:allow-grouping="false"
													width="120"
												>
													<DxLookup
														:data-source="[
															{ id: false, name: 'Terminado...' },
															{ id: true, name: 'Editando...' },
														]"
														value-expr="id"
														display-expr="name"
													/>
												</DxColumn>

												<DxColumn
													data-field="management_report_is_draft"
													caption="Informe Gestíon"
													data-type="string"
													alignment="center"
													:visible="true"
													:allow-filtering="true"
													:allow-grouping="false"
													width="120"
												>
													<DxLookup
														:data-source="[
															{ id: false, name: 'Terminado...' },
															{ id: true, name: 'Editando...' },
														]"
														value-expr="id"
														display-expr="name"
													/>
												</DxColumn>

												<DxColumn
													data-field="active"
													caption="Activo"
													data-type="bolean"
													alignment="center"
													:visible="true"
													:allow-filtering="true"
													:allow-grouping="false"
													width="120"
												>
													<DxLookup
														:data-source="[
															{ id: true, name: 'Si' },
															{ id: false, name: 'No' },
														]"
														value-expr="id"
														display-expr="name"
													/>
												</DxColumn>

												<DxColumn :width="130" alignment="center" :allow-filtering="false" cell-template="tpl" caption="" />

												<template #tpl="{ data }">
													<span class="cmds">
														<span v-if="true">
															<a
																v-if="data.data.is_draft && editMode"
																title="Completar Campos... "
																class="cmd-item color-main-600"
																@click.prevent="
																	go(data.data.id, `/unidad/${data.data.research_group_id}/plan_accion/${data.data.id}/datos`, 'Cargando Ingreso de Datos')
																"
																href="#"
															>
																<i class="icon-pencil"></i>
															</a>

															<a
																v-if="!data.data.is_draft && editMode"
																title="Completar Informe... "
																class="cmd-item color-main-600"
																@click.prevent="
																	go(data.data.id, `/unidad/${data.data.research_group_id}/plan_accion/${data.data.id}/datos`, 'Cargando Ingreso de Datos')
																"
																href="#"
															>
																<i class="icon-clipboard5"></i>
															</a>

															<a
																title="Vista Impresion... "
																class="cmd-item color-main-600"
																@click.prevent="
																	go(data.data.id, `/unidad/${data.data.research_group_id}/plan_accion/${data.data.id}/vista`, 'Cargando Vista de Datos')
																"
																href="#"
															>
																<i class="icon-eye"></i>
															</a>

															<template v-if="editMode">
																<a
																	title="Activar o desactivar"
																	v-if="data.data.active"
																	class="cmd-item color-main-600 mr-2"
																	@click.prevent="active(data, false)"
																	href="#"
																>
																	<i class="icon-database-remove"></i>
																</a>
																<a v-else title="Activar o desactivar" class="cmd-item color-main-600 mr-2" @click.prevent="active(data, true)" href="#">
																	<i class="icon-database-add"></i>
																</a>
															</template>

															<template v-if="isAdmin">
																<a
																	title="Desactivar Guardado Plan de Accion"
																	v-if="data.data.is_draft"
																	class="cmd-item color-main-600 mr-2"
																	@click.prevent="activePlan(data, false)"
																	href="#"
																>
																	<i class="icon-lock"></i>
																</a>
																<a
																	v-else
																	title="Habilitar Guardado Plan de Accion"
																	class="cmd-item color-main-600 mr-2"
																	@click.prevent="activePlan(data, true)"
																	href="#"
																>
																	<i class="icon-unlocked"></i>
																</a>

																<a
																	title="Desactivar Guardado informe Gestion"
																	v-if="data.data.management_report_is_draft"
																	class="cmd-item color-main-600 mr-2"
																	@click.prevent="activeInfo(data, false)"
																	href="#"
																>
																	<i class="icon-file-locked2"></i>
																</a>
																<a
																	v-else
																	title="Habilitar Guardado informe Gestíon"
																	class="cmd-item color-main-600 mr-2"
																	@click.prevent="activeInfo(data, true)"
																	href="#"
																>
																	<i class="icon-file-spreadsheet2"></i>
																</a>
															</template>
														</span>
													</span>
												</template>
											</DxDataGrid>
										</div>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>

		<DxPopup :visible="popupObs" :drag-enabled="false" :close-on-outside-click="false" :show-title="true" width="400" height="250" title="Nuevo plan de acción">
			<div>
				<div class="col">
					<p>
						<i class="icon-info mr-1 color-main-600"></i>
						<span class="font-weight-semibold"> Seleccione el año de vigencia para crear el plan de acción y presione el botón guardar.</span>
					</p>
					<DxValidationGroup ref="basicGroup">
						<div class="col-md-12">
							<div class="form-group">
								<label>Año de vigencia : </label>
								<DxSelectBox
									:show-clear-button="true"
									:grouped="false"
									:search-enabled="false"
									placeholder="Seleccione..."
									:value.sync="baseObj.execution_validity"
									class="form-control"
									:data-source="tipoproceso"
									display-expr="st_name"
									value-expr="id"
									:wrapItemText="true"
								>
									<DxValidator>
										<DxRequiredRule />
									</DxValidator>
								</DxSelectBox>
							</div>
						</div>
					</DxValidationGroup>
				</div>
			</div>
		</DxPopup>

		<div class="row" v-if="is_dev && debug">
			<div class="col">
				<div class="card">
					<div class="card-body">
						<span class="font-weight-semibold">editMode:</span> {{ editMode }}
						<hr class="sep mb-0" />
						<span class="font-weight-semibold">group:</span>
						{{ JSON.stringify(group, null, 3) }}
					</div>
				</div>
			</div>
		</div>
	</div>
</template>
<script>
/* eslint-disable no-unused-vars */
/* eslint-disable vue/no-unused-components */
let root;

let $ = window.jQuery;
import DxStore from "@/store/dx";
import {
	DxLookup,
	DxColumn,
	DxColumnChooser,
	DxDataGrid,
	DxExport,
	DxFilterRow,
	DxGroupItem,
	DxGroupPanel,
	DxGrouping,
	DxLoadPanel,
	DxPager,
	DxPaging,
	DxSearchPanel,
	DxSorting,
	DxStateStoring,
	DxFilterPanel,
	DxSummary,
} from "devextreme-vue/data-grid";
import { DxEmailRule, DxRequiredRule, DxStringLengthRule, DxValidator, DxPatternRule } from "devextreme-vue/validator";
import { DxDateBox, DxSelectBox, DxButton, DxTagBox, DxTextBox, DxNumberBox, DxTextArea, DxValidationGroup, DxPopup } from "devextreme-vue";

import { mapActions, mapGetters, mapState } from "vuex";
import DxAccordion, { DxItem } from "devextreme-vue/accordion";

//trabajo con documentos de excel
// import { Workbook } from "exceljs";
// import { saveAs } from "file-saver-es";
// import { exportDataGrid } from "devextreme/excel_exporter";

export default {
	created: function() {
		root = this;
		root.rutaprincipal = String(location.href).slice(0, -this.$route.path.length) + "/unidad";
		root.isAdmin = this.user_role_id === this.get_role_id("administrador");
		console.warn("la ruta", this.$route);
	},
	mounted() {
		console.log("MOUNTED");
		root.loaderElement = "#panel-unidades .card-body";
		root.loaderMessage = "Cargando Histoórico...";
		root.loaderShow();
		root.unidad = window.vm.$clone(root.baseEntity);
		// root.getGroupRoles();
		// root.getFacultades();
		root.loaderHide();
		console.log("editMode", root.editMode);
	},
	data: () => ({
		popupObs: false,
		tipoproceso: [
			{ id: 2021, st_name: 2021 },
			{ id: 2022, st_name: 2022 },
		],
		rutaprincipal: null,
		group: null,
		isAdmin: false,
		totaCount: 0,
		baseObj: {
			execution_validity: null,
			is_draft: true,
			active: true,
		},
		// dataSource: data,
	}),
	components: {
		DxLookup,
		DxItem,
		DxAccordion,
		DxStateStoring,
		DxPopup,
		// DxExport,
		DxButton,
		DxFilterPanel,
		DxColumn,
		DxPatternRule,
		DxColumnChooser,
		DxDataGrid,
		DxDateBox,
		DxNumberBox,
		DxFilterRow,
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
	},
	computed: {
		// ...mapGetters("core/tipo", ["subtypesByType"]),
		// ...mapState("unidad/colciencias", { convocatorias: "items" }),
		dataSource: function() {
			// if (typeof this.group.id === "undefined") return null;
			console.log("root.group", this.group);
			return DxStore({
				key: ["id"],
				// ids: ["dw_type_id=1"],
				stringParam: "dw_type_id=" + root.tipos,
				endPoint: `action_plans`,
				onLoading: function(loadOptions) {
					root.loaderShow("Cargando elementos", "#panel-produccion .card-body");
				},
				onLoaded: function(results, baseEntity) {
					// // console.clear();
					console.log("results", results);
					root.totaCount = results.totalCount;
					root.loaderHide();
				},
			});
		},
	},
	methods: {
		// ...mapActions("unidad", ["getUnit", "getResearchers", "saveResearcher", "updateResearcher"]),
		...mapActions("unidad/producto/universalSentUpAct", {
			objSave: "save",
			objUpdate: "update",
			elementoActive: "active",
		}),

		/*
    excelDoc(e) {
      const workbook = new Workbook();
      const worksheetx = workbook.addWorksheet('Planes e Informes');
      console.clear();
      this.$info("imprimiendo excel");
      
      console.warn("worksheetx: ",worksheetx);
      console.warn("e: ",e);
      exportDataGrid({
         worksheet: worksheetx, 
         component: e.component
      }).then(() => {
        workbook.xlsx.writeBuffer().then((buffer) => {
          saveAs(new Blob([buffer], { type: 'application/octet-stream' }), 'historico.xlsx');
        });
      });
      e.cancel = true;
    },*/

		estadoEntrega(cellInfo) {
			return cellInfo.value ? "Editando..." : "Terminado";
		},

		nuevo() {
			let msg = "¿Está seguro que desea crear un nuevo plan de acción?<br>Nota: Recuerde que este no podrá ser eliminado o editado.";
			this.$confirm(msg, function(si_no) {
				console.log("result nuevo plan", si_no);
				if (si_no) {
					root.popupObs = true;
					//root.nuevoPlan();
				}
			});
		},

		active(data, state) {
			// // console.clear();
			console.log("active", data);
			console.log("state", state);
			let a = state ? "activar" : "desactivar";
			let am = state ? "Activando" : "Desactivando";
			let msg = `¿Realmente desea ${a} el plan de acción: <span class='text-sb'>"${data.data.execution_validity} id: ${data.data.id}"</span>?`;
			this.$confirm(msg, function(si_no) {
				console.log("result", si_no);
				if (si_no) {
					root.loaderShow(`${am}`, root.panelGrid);
					var dto = {
						url: `action_plans/${data.data.id}`,
						data: {
							action_plan: {
								active: state,
								updated_by: root.user_id,
							},
						},
						cb: function(result) {
							console.log("Result", result);
							root.grid.refresh();
							root.loaderHide();
						},
					};
					console.log("dto", dto);
					root.elementoActive(dto);
					root.loaderHide();
				}
			});
		},

		activeInfo(data, state) {
			// // console.clear();
			console.log("active", data);
			console.log("state", state);
			let a = state ? "activar" : "desactivar";
			let am = state ? "Activando" : "Desactivando";
			let msg = `¿Realmente desea ${a} el guardado de datos en Informe de Gestíon <span class='text-sb'>"${data.data.execution_validity} id: ${data.data.id}"</span>?`;

			if (data.data.is_draft == false) {
				this.$confirm(msg, function(si_no) {
					console.log("result", si_no);
					if (si_no) {
						let baseObjx = {};
						root.loaderShow(state + " Informe");
						baseObjx.update_by = root.user_id;
						baseObjx.updated_by = root.user_id;
						baseObjx.management_report_is_draft = state;
						let obj = baseObjx;
						let dto = {
							unidadId: data.data.research_group_id,
							stringEP: "ap_management_reports",
							mod: data.data.id,
							newFormat: true,
							objectSend: { ap_management_report: obj },
							cb: function(item) {
								console.log("dato", item);
								root.grid.refresh();
								// root.$router.go('/unidad/'+root.data.data.research_group_id+'/plan_accion');
								//root.go(0, `/unidad/${root.data.data.research_group_id}/plan_accion`, 'Cargando Ingreso de Datos');
								root.loaderHide();
							},
						};
						root.objUpdate(dto);
					}
				});
			} else {
				root.$info("Debe terminar el plan de acción para continuar con esta tarea.");
			}
		},

		activePlan(data, state) {
			//console.clear();
			console.log("active", data);
			console.log("state", state);
			let a = state ? "activar" : "desactivar";
			let am = state ? "Activando" : "Desactivando";
			let msg = `¿Realmente desea ${a} el guardado de datos en plan de acción con <span class='text-sb'>"${data.data.execution_validity} id: ${data.data.id}"</span>?`;
			this.$confirm(msg, function(si_no) {
				console.log("result", si_no);
				if (si_no) {
					root.loaderShow("Activando formularios");
					root.baseObj.update_by = root.user_id;
					root.baseObj.updated_by = root.user_id;

					if (data.data.execution_validity == 0) root.baseObj.execution_validity = 2021;
					else root.baseObj.execution_validity = data.data.execution_validity;
					root.baseObj.is_draft = state;
					root.baseObj.active = true;
					let obj = root.baseObj;
					let dto = {
						unidadId: data.data.research_group_id,
						stringEP: "action_plans",
						mod: data.data.id,
						newFormat: true,
						objectSend: { action_plan: obj },
						cb: function(item) {
							console.log("dato", item);
							root.grid.refresh();
							// root.$router.go('/unidad/'+root.data.data.research_group_id+'/plan_accion');
							//root.go(0, `/unidad/${root.data.data.research_group_id}/plan_accion`, 'Cargando Ingreso de Datos');
							root.loaderHide();
						},
					};
					root.objUpdate(dto);

					if (data.data.management_report_is_draft == false) {
						let baseObjx = {};
						root.loaderShow(state + "Activando Informe");
						baseObjx.update_by = root.user_id;
						baseObjx.updated_by = root.user_id;
						baseObjx.management_report_is_draft = true;
						let objx = baseObjx;
						let dtox = {
							unidadId: data.data.research_group_id,
							stringEP: "ap_management_reports",
							mod: data.data.id,
							newFormat: true,
							objectSend: { ap_management_report: objx },
							cb: function(item) {
								console.log("dato", item);
								root.grid.refresh();
								// root.$router.go('/unidad/'+root.data.data.research_group_id+'/plan_accion');
								//root.go(0, `/unidad/${root.data.data.research_group_id}/plan_accion`, 'Cargando Ingreso de Datos');
								root.loaderHide();
							},
						};
						root.objUpdate(dtox);
					}
				}
			});
		},
		gridInit(e) {
			let root = this;
			root.grid = e.component;
			// 202103311458: https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxDataGrid/Events/
			root.grid.on({
				contentReady: (e) => {
					console.log("contentReady", e);
					if (root.mode == null) root.loaderHide();
				},
				optionChanged: (e) => {
					if (e.fullName == "paging.pageIndex") {
						console.log("optionChanged", e);
						root.loaderShow();
					}
				},
				editorPreparing: (e) => {
					if (e.dataField === "LastName" && e.parentType === "dataRow") {
						e.editorOptions.disabled = e.row.data && e.row.data.FirstName === "";
					}
				},
			});
		},
		onContentReady(e) {
			console.log(root._sep);
			console.log("onContentReady!", e);
		},
		cmdClick(e, data) {
			// // console.clear();
			let action = e.itemData.command;
			console.log("action", action);
			console.log("data", data);
			if (action === "edit" || action === "parent_type" || action === "type") root.edit(action, data.data);
			else if (action === "enable" || action === "disable") root.enable(action === "enable", data.data);
		},
		cmdGet(data) {
			let details = data.data;
			let cmds = [];
			if (details.type_id !== null) {
				cmds.push({
					command: "type",
					text: `Editar tipo "${details.type_name}"...`,
					icon: "icon-database-edit",
				});
			}
			if (details.parent_id !== null) {
				cmds.push({
					command: "parent_type",
					text: `Editar subtipo Padre "${details.parent_name}"...`,
					icon: "icon-database-edit",
				});
			}
			if (details.id !== null) {
				cmds.push({
					command: "edit",
					text: `Editar subtipo "${details.name}"...`,
					icon: "icon-database-edit",
				});
			}
			let enable = {
				command: "enable",
				text: `Activar subtipo "${details.name}"...`,
				icon: "icon-database-check",
			};
			let disable = {
				command: "disable",
				text: `Desactivar subtipo "${details.name}"...`,
				icon: "icon-database-remove",
			};
			cmds.push(details.active ? disable : enable);

			return cmds;
		},
	},
};
</script>
