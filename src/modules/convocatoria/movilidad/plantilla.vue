<template>
	<div>
		
			<div class="col-12">
				<div class="card" id="panel-documentos">
					<div class="card-body group-detail mh">
						<div class="row pb-3 mb-2 bb">
							<div class="col">
								<div class="col d-flex justify-content-between align-items-end">
									<div>
										<span class="title d-block"><i class="icon-pencil6"></i> Convocatoria {{
												item.call_code
										}}</span>
										<span class="title2 d-block mt-2">{{ item.call_name }}</span>
									</div>
									<div class="sub-title text-nowrap ml-4"><i
											class="icon-page-break"></i>&nbsp;Plantilla de documento</div>
								</div>
							</div>
						</div>
						<div class="row">
							<div class="col-3">
								<div class="row pb-2 mb-2 bb">
									<div class="col font-weight-semibold color-main-500">
										ESTRUCTURA DE DOCUMENTO
									</div>
								</div>
								<div class="row">
									<div class="col">
										<DxSortable group="shared" data="document_items" filter=".dx-treeview-item"
											:allow-drop-inside-item="true" :allow-reordering="true"
											@drag-change="onDragChange" @drag-end="onDragEnd">
											<DxTreeView :expand-nodes-recursive="false" :items="document_items"
												:select-by-click="true" :select-nodes-recursive="false"
												@item-context-menu="treeViewItemContextMenu"
												@selection-changed="treeViewSelectionChanged" data-structure="plain"
												key-expr="id" display-expr="ch_title" parent-id-expr="ch_parent_id"
												id="treeview" ref="treeviewRef" selection-mode="single"
												show-check-boxes-mode="none">
												<!-- show-check-boxes-mode="selectAll, normal, none" -->
												<template #item="item">
													<span class="list-item">
														<span class="numeral">{{ item.data.numeral }}.</span><span>{{
																item.data.ch_title
														}}</span>
													</span>
												</template>
											</DxTreeView>
										</DxSortable>
									</div>
								</div>
								<div class="row pt-2 pb-2 mt-2 mb-2 bt bb">
									<div class="col">
										<a href="#" @click.prevent="renderDoc" title="Exportar Plantilla a MS Word..."
											class="btn btn-sm btn-main btn-labeled btn-labeled-left legitRipple w-100"><b><i
													class="icon-file-word"></i></b>&nbsp;Exportar</a>
									</div>
									<!-- <div class="col">
										<a
											href="#"
											@click.prevent="saveDoc"
											title="Guardar Plantilla..."
											class="btn btn-sm btn-main btn-labeled btn-labeled-right legitRipple w-100"
											><b><i class="icon-floppy-disk"></i></b>&nbsp;Guardar</a
										>
									</div> -->
								</div>
							</div>
							<div class="col" v-if="selectedItem">
								<div class="row">
									<div class="col sub-title color-main-600 mb-3">{{ selectedItem.numeral }}. {{
											selectedItem.ch_title
									}}</div>
								</div>
								<div class="row">
									<div class="col">
										<HtmlEditor :height="600" :sync-obj="selectedItem" sync-prop="ch_description"
											ref="HtmlEditorRef" />
									</div>
								</div>
							</div>
							<div class="col" v-else>
								<div class="row mt-5">
									<div class="col mt-5 title2">
										<i class="icon-point-left mr-2 d-inline-block"></i>Haga clic en algún ítem para
										editarlo o arrastre y suelte para modificar su orden.
									</div>
								</div>
								<div class="row">
									<div class="col pt-2 pl-4 font-weight-semibold">(clic derecho para más opciones)
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>	
				
			
		<DxContextMenu ref="contextMenu" :data-source="contextMenuItems" target="#treeview .dx-treeview-item"
			@item-click="contextMenuItemClick" />

	</div>
</template>
<script>
/* eslint-disable no-unused-vars */
/* eslint-disable no-inner-declarations */
/* eslint-disable vue/no-unused-components */
// 202108180553: https://js.devexpress.com/Demos/WidgetsGallery/Demo/TreeView/DragAndDropHierarchicalDataStructure/Vue
// 202108180602: https://js.devexpress.com/Demos/WidgetsGallery/Demo/TreeView/ItemSelectionAndCustomization/Vue
// 202109240240: https://js.devexpress.com/Demos/WidgetsGallery/Demo/TreeView/ContextMenuIntegration/Vue
// 202109240601: https://js.devexpress.com/Demos/WidgetsGallery/Demo/TreeView/DragAndDropPlainDataStructure/Vue
// 202110052332: https://github.com/guigrpa/docx-templates
// 202110070500: https://js.devexpress.com/Demos/WidgetsGallery/Demo/HtmlEditor/Tables/Vue/Light/
let $ = window.jQuery,
	root = null;
import createReport from "docx-templates";
import * as TextBox from "devextreme/ui/text_box";
import { mapActions, mapGetters } from "vuex";
import DxTreeView from "devextreme-vue/tree-view";
import DxSortable from "devextreme-vue/sortable";
import { DxCheckBox } from "devextreme-vue/check-box";
import DxContextMenu from "devextreme-vue/context-menu";
import { DxHtmlEditor, DxToolbar, DxItem } from "devextreme-vue/html-editor";

export default {
	name: "datosBasicos",
	components: {
		DxHtmlEditor,
		DxToolbar,
		DxItem,
		DxTreeView,
		DxSortable,
		DxContextMenu,
		DxCheckBox,
		// Header: () => import("./_header"),
		HtmlEditor: () => import("@/components/element/html_editor_v2"),
	},
	props: {
		id_convocatoria: {
			type: Number,
			default: () => 0,
		},
	},
	data: () => ({
		htmlEditor: null,
		treeAction: null,
		textboxId: "tb-container",
		currentName: null,
		currentItem: null,
		currentItemEl: null,
		textBoxElHtml: null,
		criteria: null,
		financing: null,
		activities: null,
		documents: null,
		items: null,
		clearButton: {
			icon: "clear",
			name: "saveBtn",
			location: "after",
			stylingMode: "text",
			elementAttr: {
				class: "currency",
			},
			onClick: (e) => {
				// console.clear();
				// console.log("e", e.component.option("text"));
			},
		},
		saveButton: {
			icon: "save",
			name: "saveBtn",
			location: "after",
			stylingMode: "text",
			elementAttr: {
				class: "currency",
			},
			onClick: (e) => {
				// console.clear();
				// console.log("e", e.component.option("text"));
			},
		},
		textBox: null,
		treeView: null,
		item: null,
		baseObj: {
			observation: null,
		},
		document_items: [],
		document_items_all: [],
		id: "panel-convocatoria-documentos",
		selectedItem: null,
		contextMenuItems: [
			{ id: "rename", text: "Renombrar", icon: "rename" },
			{ id: "add", text: "Agregar", icon: "add" },
			{ id: "delete", text: "Desactivar", icon: "close" },
		],
	}),
	methods: {
		...mapActions("core/capitulo", { capitulos: "getAll", crearCapitulos: "createAll", guardarCapitulos: "updateAll", guardarCapitulo: "update" }),
		...mapActions("convocatoria", ["getItem", "getTemplate", "getCriteria", "getFinancingItems", "getActivities", "getDocuments", "getItems"]),
		setNumerals(parent_id) {
			if (typeof parent_id === "undefined") parent_id = null;
			root.document_items
				.filter((o) => o.ch_parent_id === parent_id)
				.forEach((item, index) => {
					let num = index + 1;
					item["expanded"] = true;
					item["isDirectory"] = root.document_items.filter((o) => o.ch_parent_id === item.id).length > 0;
					item["numeral"] = (parent_id !== null ? root.document_items.filter((o) => o.id === parent_id)[0].numeral + "." : "") + num;
					root.setNumerals(item.id);
				});
		},
		saveItem: (e) => {
			// console.clear();
			// console.log("e =>", e);
			let n = e.component.option("value");
			// console.log("n =>", n);
			if (n.length > 0)
				if (root.treeAction === "rename") {
					root.selectedItem.itemData.ch_title = n;
					root.treeView.selectItem(root.selectedItem.itemData.id);
				} else if (root.treeAction === "add") {
					// console.clear();
					// let ids = root.document_items.map((o) => o.id).sort()[0];
					// console.log("id =>", ids);
					// root.document_items.forEach((item) => {
					// 	ids.push(item.id);
					// });
					let n_id = 0;
					let ids = root.document_items.map((o) => parseInt(o.id));
					console.log("ids =>", ids);
					ids.forEach((id) => {
						if (id >= n_id) n_id = id + 1;
					});
					// let n_id = ids[ids.length - 1] + 1;
					// 202110051249: Find index https://stackoverflow.com/a/39529049
					var index = root.document_items.findIndex((o) => o.id == root.selectedItem.itemData.id) + 1;
					// console.log("index =>", index);
					let ni = {
						id: n_id,
						ch_field: null,
						ch_parent_id: null,
						numeral: "XX",
						ch_order: 0,
						ch_title: n,
						ch_description: "--",
						is_new: true,
						created_by: root.user_id,
					};
					console.log("nuevo =>", ni);
					// 202110051246: Add item to specific index https://stackoverflow.com/a/586189
					root.document_items.splice(index, 0, ni);
					// console.log("Agregar =>", ni);
					root.setNumerals();
					setTimeout(function () {
						root.treeView.selectItem(n_id);
						root.treeViewSelectionChanged(e);
					}, 200);
				}
			root.cancelItem(e);
		},
		cancelItem: (e) => {
			try {
				root.selectedItem = null;
				root.currentItemEl.show();
				$("#" + root.textboxId).remove();
				setTimeout(function () {
					root.treeViewSelectionChanged(e);
				}, 100);
				// console.log("cancelItem e =>", e);
			} catch (error) {
				// console.log("error =>", error);
			}
		},
		treeViewItemContextMenu(e) {
			// console.clear();
			root.selectedItem = e;
			// console.log("root.selectedItem =>", root.selectedItem.itemData.ch_title);
		},
		contextMenuItemClick: async (e) => {
			// console.log(root.$sep);
			// console.log("e =>", e);
			root.currentItemEl = $(root.selectedItem.itemElement);
			$("#" + root.textboxId).remove();
			$("body").append(`<div id="${root.textboxId}" class="slide"></div>`);
			let instance = new TextBox("#" + root.textboxId, {
				id: "txt-field",
				value: root.selectedItem.itemData.ch_title,
				showClearButton: true,
				placeholder: "Nombre",
				onEnterKey: root.saveItem,
				onFocusOut: root.cancelItem,
				blur: root.cancelItem,
				deferRendering: false,
				visible: true,
			});
			switch (e.itemData.id) {
				case "rename": {
					root.treeAction = "rename";
					root.currentItemEl.after(instance.element()).focus();
					instance
						.element()
						.querySelector(".dx-texteditor-input")
						.select();
					root.currentItemEl.hide();
					break;
				}
				case "add": {
					root.treeAction = "add";
					root.currentItemEl.after(instance.element());
					instance.option({ value: "", placeholder: "Título para el nuevo ítem" });
					instance.focus();
					break;
				}
				case "delete": {
					root.treeAction = null;
					root.$confirm(`¿Realmente desea desactivar el ítem "${root.selectedItem.itemData.ch_title}"?`, async function (si) {
						// console.log("result", si);
						if (si) {
							root.loaderShow("Desactivando ítem", "#panel-documentos .card-body");
							console.clear();
							console.log("DELETE!");
							let to_send = root.selectedItem.itemData;
							to_send["active"] = false;
							to_send["udpated_by"] = root.user_id;
							let res = await root.guardarCapitulo({
								id: root.item.id,
								context: "calls",
								item: to_send,
							});
							console.log("res =>", res);
							root.document_items = root.document_items.filter((o) => o.id !== root.selectedItem.itemData.id);
							root.setNumerals();
							setTimeout(function () {
								root.loaderHide();
							}, 1000);
						}
					});
					break;
				}
			}
		},
		treeViewSelectionChanged(e) {
			// console.clear();
			console.log("e =>", e);
			let se = root.treeView.getSelectedNodes().map((node) => node.itemData)[0];
			if (typeof se !== "undefined") {
				root.selectedItem = root.treeView.getSelectedNodes().map((node) => node.itemData)[0];
				if (root.htmlEditor !== null) root.htmlEditor.setMarkup(root.selectedItem.ch_description);
			}
		},
		treeViewContentReady(e) {
			root.selectedItem = e.component.getSelectedNodes().map((node) => node.itemData);
			// console.log("root.selectedItem =>", root.selectedItem);
		},
		move(arr, old_index, new_index) {
			if (new_index >= arr.length) {
				var k = new_index - arr.length + 1;
				while (k--) {
					arr.push(undefined);
				}
			}
			arr.splice(new_index, 0, arr.splice(old_index, 1)[0]);
			// return arr; // for testing
		},
		onDragChange(e) {
			// console.log("e =>", e);
			// if (e.fromComponent === e.toComponent) {
			// 	const fromNode = root.findNode(root.getTreeView(e.fromData), e.fromIndex);
			// 	const toNode = root.findNode(root.getTreeView(e.toData), root.calculateToIndex(e));
			// 	if (toNode !== null && root.isChildNode(fromNode, toNode)) {
			// 		e.cancel = true;
			// 	}
			// }
		},
		onDragEnd(e) {
			// console.clear();
			if (e.fromIndex === e.toIndex) return;
			const fromNode = root.findNode(e.fromIndex);
			const toNode = root.findNode(root.calculateToIndex(e));
			root.moveNode(fromNode, toNode, e.dropInsideItem);
			root.setNumerals();
		},
		calculateToIndex(e) {
			if (e.fromComponent != e.toComponent || e.dropInsideItem) return e.toIndex;
			return e.fromIndex >= e.toIndex ? e.toIndex : e.toIndex + 1;
		},
		findNode(index) {
			const nodeElement = root.treeView.element().querySelectorAll(".dx-treeview-node")[index];
			return nodeElement ? root.findNodeById(root.treeView.getNodes(), nodeElement.getAttribute("data-item-id")) : null;
		},
		findNodeById(nodes, id) {
			for (var i = 0; i < nodes.length; i++) {
				if (nodes[i].itemData.id == id) {
					return nodes[i];
				}
				if (nodes[i].children) {
					const node = root.findNodeById(nodes[i].children, id);
					if (node != null) {
						return node;
					}
				}
			}
			return null;
		},
		moveNode(fromNode, toNode, isDropInsideItem) {
			const fromIndex = root.document_items.findIndex((item) => item.id == fromNode.itemData.id);
			root.document_items.splice(fromIndex, 1);
			const toIndex = toNode === null || isDropInsideItem ? root.document_items.length : root.document_items.findIndex((item) => item.id == toNode.itemData.id);
			root.document_items.splice(toIndex, 0, fromNode.itemData);
			root.moveChildren(fromNode);
			if (isDropInsideItem) {
				fromNode.itemData.ch_parent_id = toNode.itemData.id;
			} else {
				fromNode.itemData.ch_parent_id = toNode != null ? toNode.itemData.ch_parent_id : undefined;
			}
		},
		moveChildren(node) {
			if (!node.itemData.isDirectory) return;
			node.children.forEach((child) => {
				if (child.itemData.isDirectory) {
					root.moveChildren(child);
				}
				const fromIndex = root.document_items.findIndex((item) => item.id == child.itemData.id);
				root.document_items.splice(fromIndex, 1);
				root.document_items.splice(root.document_items.length, 0, child.itemData);
			});
		},
		isChildNode(parentNode, childNode) {
			let parent = childNode.parent;
			while (parent !== null) {
				if (parent.itemData.id === parentNode.itemData.id) return true;
				parent = parent.parent;
			}
			return false;
		},
		// 202110070110: Guarda estructura
		saveDoc: async () => {
			root.loaderShow("Guardando Plantilla", "#panel-documentos .card-body");
			const results = await root.guardarCapitulos({
				id: root.item.id,
				context: "calls",
				items: root.document_items,
			});
			console.log("results =>", results);
			root.loaderHide();
		},
		// 202110061902: https://github.com/guigrpa/docx-templates/tree/d978ff7a294a9c9516f19fe5c030ab84a1a8cbbc#browser-usage
		renderDoc: async () => {
			// console.clear();
			root.loaderShow("Generando Documento", "#panel-documentos .card-body");
			const template = await fetch(`${root.baseUrl}data/convocatoria.docx`).then((res) => res.arrayBuffer());
			let item = root.$clone(root.item);
			item.call_name = item.call_name.toUpperCase();
			root.document_items.forEach((item) => {
				item["ch_description_html"] = `<meta charset="UTF-8">
					<body>
					<style>
						html, body, td, p { font-family:Calibri,sans-serif; font-size:12.5px; text-align:justify; }
						table tr:first-child td { font-weight: 600 !important; }
						table { width:100%; }
					</style>
					${item.ch_description}
				</body>`;
			});
			console.log("root.document_items =>", root.document_items);
			const document = await createReport({
				template,
				cmdDelimiter: ["{", "}"],
				data: {
					item: item,
					chapters: root.document_items,
				},
			});
			let blob = new Blob([document], { type: "application/vnd.openxmlformats-officedocument.wordprocessingml.document" });
			let url = window.URL.createObjectURL(blob);
			root.downloadURL(url, root.$getTS(new Date(), true) + "-convocatoria.docx");
		},
		downloadURL: (data, fileName) => {
			const a = document.createElement("a");
			a.href = data;
			a.download = fileName;
			document.body.appendChild(a);
			a.style = "display: none";
			a.click();
			a.remove();
			setTimeout(function () {
				root.loaderHide();
			}, 800);
		},
		setContents: (items) => {
			if (typeof items == "undefined") items = root.document_items;
			items.forEach((item) => {
				// 202110070101: Se usa 'item.ch_order' para la verificación de los campos
				if (item.ch_order == 1) {
					// Dirigida a
					item.ch_description = root.item.call_directed_towards;
				} else if (item.ch_order == 3) {
					// Objetivos
					item.ch_description = root.item.call_objective;
				} else if (item.ch_order == 8) {
					// Cronograma
					if (root.activities.filter((o) => o.active).length > 0) {
						let html = `<table><tr><td width="40%">Actividad</td><td width="30%">Responsable</td><td>Fecha inicio</td><td>Fecha cierre</td></tr><tbody>`;
						root.activities
							.filter((o) => o.active)
							.forEach((o) => {
								html += `<tr><td>${o.sa_description}</td><td>${o.sa_responsible}</td><td>${o.sa_start_date}</td><td>${o.sa_end_date}</td></tr>`;
							});
						html += `</tbody></table>`;
						item.ch_description = html;
					}
				} else if (item.ch_order == 9) {
					// Cuantía
					let html = `<p>La cuantía de la presente convocatoria será asignada con cargo al rubro de Promoción de la Investigación de la siguiente manera:</p><table cellspacing='0'><tr><td><p>Monto máximo a financiar por proyecto:</p></td><td><p>$${root.$format(
						root.item.call_max_budget_per_project
					)}</p></td></tr><tr><td><p>Monto total a financiar en la convocatoria</p></td><td><p><b>$${root.$format(
						root.item.call_global_budget
					)}</b></p></td></tr></table>`;
					item.ch_description = html;
				} else if (item.ch_order == 10) {
					// Criterios
					if (root.criteria.filter((o) => o.active).length > 0) {
						let html = `<table><tr><td width="40%">Criterio</td><td width="30%">Porcentaje</td></tr><tbody>`;
						root.criteria
							.filter((o) => o.active)
							.forEach((o) => {
								html += `<tr><td>${o.eval_criterion_name}</td><td>${root.$format(o.cec_percentage)}%</td></tr>`;
							});
						html += `</tbody></table>`;
						item.ch_description = html;
					}
				} else if (item.ch_order == 12) {
					// Documentos
					if (root.documents.filter((o) => o.active).length > 0) {
						let html = `<p>Los documentos solicitados para la convocatoria son:</p><ol>`;
						root.documents
							.filter((o) => o.active)
							.forEach((o) => {
								html += `<li>${o.document_name}</li>`;
							});
						html += `</ol>`;
						item.ch_description = html;
					}
				} else if (item.ch_order == 15) {
					// Rubros
					if (root.items.filter((o) => o.active).length > 0) {
						let html = `<table><tr><td width="40%">Rubro a financiar</td><td width="30%">Porcentaje</td><td width="30%">Monto máximo</td><td width="30%">% máximo</td></tr><tbody>`;
						root.items
							.filter((o) => o.active)
							.forEach((o) => {
								html += `<tr><td>${o.item_name}</td><td>${root.$format(o.ci_percentage)}%</td><td>$${root.$getAmmount(
									root.item.call_max_budget_per_project,
									o.ci_maximum_percentage,
									true
								)}</td><td>${root.$format(o.ci_maximum_percentage)}%</td></tr>`;
							});
						html += `</tbody></table>`;
						item.ch_description = html;
					}
				}
			});
		},
	},
	computed: {
		...mapGetters("core/tipo", ["subtypesByType"]),
	},
	created: async function () {
		root = this;
		//let uId = root.$route.params.itemId;
		let uId = root.id_convocatoria;
		root.item = await root.getItem(uId);
		console.log("root.item =>", root.item);
		document.title += ` Convocatoria ${root.item.call_code}`;
		// 202110070204: Timeout para el 'root.$refs'
		setTimeout(async function () {
			root.treeView = root.$refs.treeviewRef.instance;
			root.criteria = await root.getCriteria(uId);
			root.financing = await root.getFinancingItems(uId);
			root.activities = await root.getActivities(uId);
			root.documents = await root.getDocuments(uId);
			root.items = await root.getItems(uId);
			// 202110070120: Carga capítulos guardados
			// console.clear();
			let caps = await root.capitulos({ context: "calls", id: uId });
			console.log("CAPS EN BD =>", caps);
			// Determina si ya se crearon capítulos
			if (caps.length > 0) {
				caps.forEach((item, idx) => {
					if (item.updated_by == null) item.updated_by = root.user_id;
					item.is_new = false;
				});
				root.document_items_all = caps;
				// 202110102133: Solo los activos
				root.document_items = caps.filter((o) => o.active);
				root.setContents();
			} else {
				// 202110070201: Si no hay items creados los crea
				caps = await root.getTemplate();
				root.setContents(caps);
				caps.forEach((item, idx) => {
					// item.id = idx + 1;
					item["created_by"] = root.user_id;
					item["updated_by"] = root.user_id;
					item["id_orig"] = item.id;
					item["id_parent"] = item.ch_parent_id;
				});
				console.log("caps =>", caps);
				root.document_items = await root.crearCapitulos({
					id: root.item.id,
					context: "calls",
					items: caps,
				});
				console.log("caps creados =>", root.document_items);
			}
			root.setNumerals();
			root.loaderHide();
			setTimeout(function () {
				root.treeView.selectItem(root.document_items[0].id);
				root.treeViewSelectionChanged();
				setTimeout(function () {
					root.htmlEditor = root.$refs.HtmlEditorRef;
					console.log("root.htmlEditor =>", root.htmlEditor);
					root.htmlEditor.test();
				}, 500);
			}, 500);
		}, 500);
	},
	updated: () => {
		// console.log(root.$sep);
		// console.log("documentos Updated");
	},
	mounted: () => {
		// console.clear();
		// setTimeout(function() {
		// 	let id = `#${root.id}`;
		// 	// console.log("id", id);
		// 	$(id).fadeIn();
		// }, 700);
		root.loaderShow("Cargando Plantilla");
	},
};
</script>
