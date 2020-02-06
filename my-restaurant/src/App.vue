<template>
	<div id="app">
		<div id="nav">
			<router-link to="/">Subir Imagen</router-link>
			|
			<router-link to="/about">Acerca de</router-link>
		</div>
		<router-view />
		<br />

		<input type="file" @change="onFileSelected" />
		<br />
		<button @click="onUpload">Subir Archivo</button>
		<br />
		<img width="40%" :src="this.picture" />
	</div>
</template>
<script>
import firebase from "firebase";
import Swal from "sweetalert2";
import Vue from "vue";
import vuetify from "@/plugins/vuetify";

export default {
	name: "app",
	data() {
		return {
			selectedFile: null,
			picture: null
		};
	},
	methods: {
		onFileSelected(event) {
			this.selectedFile = event.target.files[0];
		},
		onUpload() {
			const storageRef = firebase.storage().ref(`/img/main-course/${this.selectedFile.name}`);
			const task = storageRef.put(this.selectedFile);
			const Swal = require("sweetalert2");
			task.on(
				"state_changed",
				(snapshot) => {
					let timerInterval;
					Swal.fire({
						title: "Se está subiendo el archivo",
						html: "Por favor espere un momento mientras se actualiza la imagen.",
						timerProgressBar: true,
						onBeforeOpen: () => {
							Swal.showLoading();
						},
						onClose: () => {
							clearInterval(timerInterval);
						}
					}).then((result) => {
						/* Read more about handling dismissals below */
						if (result.dismiss === Swal.DismissReason.timer) {
						}
					});
				},
				(error) => {
					console.log("error", error.message),
						() => {
							task.snapshot.ref.getDownloadURL().then((url) => {
								this.picture = url;
								console.log(this.picture);
							});
						};
				},
				() => {
					Swal.fire({
						icon: "success",
						title: "Se ha subido la imagen correctamente.",
						showConfirmButton: false,
						timer: 1500,
						onClose: () => {
							location.reload();
						}
					});
				}
			);
		}
	}
};
</script>
