<template>
	<div v-if="group">
		<Header :group="group" />
		<div class="row">
			<div class="col">
				<div class="card" id="panel-documentos">
					<div class="card-body group-detail mh">
						<div class="row mb-3">
							<div class="col">
								<div class="col d-flex justify-content-between align-items-end">
									<div class="title"><i class="icon-books"></i> {{ $titleCase(group.name) }}</div>
									<div class="sub-title"><i class="icon-file-pdf"></i> Documentos</div>
								</div>
							</div>
						</div>
						<Documentos
							:id="id"
							:parent="this"
							:main-obj="group"
							:show-on-load="true"
							:tipos="tiposDocumento"
							end-point="research_units"
							lock-element="#panel-documentos .card-body"
						/>
					</div>
				</div>
			</div>
		</div>
		<div class="row" v-if="is_dev && debug">
			<div class="col">
				<div class="card">
					<div class="card-body">
						<span class="font-weight-semibold">editMode:</span> {{ editMode }}
						<hr class="sep" />
						<span class="font-weight-semibold">group:</span> {{ JSON.stringify(group, null, 3) }}
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
// https://vuejs.org/v2/guide/single-file-components.html#What-About-Separation-of-Concerns
let $ = window.jQuery,
	root = null;
import { mapActions, mapGetters } from "vuex";
let hideErrors = () => {
	$("#panel-basicos .dx-fileuploader-files-container").hide();
	$("#panel-basicos .dx-invalid-message").hide();
	$("#panel-basicos .dx-invalid").removeClass("dx-invalid");
};

export default {
	name: "datosBasicos",
	components: {
		Header: () => import("./_header"),
		Documentos: () => import("@/components/element/documentos"),
	},
	data: () => ({
		group: null,
		id: "panel-unidad-documentos",
	}),
	methods: {
		...mapActions("unidad", ["getUnit"]),
	},
	computed: {
		...mapGetters("core/tipo", ["subtypesByType"]),
	},
	created: function() {
		root = this;
		console.log(root.$sep);
		root.tiposDocumento = root.subtypesByType("unidad_tipo_documento");
		console.log("root.tiposDocumento", root.tiposDocumento);
		let uId = root.$route.params.unidadId;
		root.getUnit({
			id: uId,
			cb: function(result) {
				root.group = result;
				document.title += ` ${root.$titleCase(root.group.name)}`;
			},
		});
	},
	updated: () => {
		console.log(root.$sep);
		console.log("documentos Updated");
		hideErrors();
	},
	mounted() {
		// setTimeout(function() {
		// 	let id = `#${root.id}`;
		// 	console.log("id", id);
		// 	$(id).fadeIn();
		// }, 700);
	},
};
</script>
