<template>
	<div id="app">
		<div id="nav">
			<router-link to="/">Subir Imagen</router-link>
			|
			<router-link to="/about">Acerca de</router-link>
		</div>
		<router-view />
		<progress :value="uploadValue" max="100" id="uploader">0%</progress>
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

export default {
	name: "app",
	data() {
		return {
			selectedFile: null,
			uploadValue: 0,
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
					/* let porcentaje = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
					this.uploadValue = porcentaje; */
				},
				(error) => {
					console.log("error", error.message),
						() => {
							this.uploadValue = 100;

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
						timer: 1000,
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
<style>
#app {
	font-family: "Avenir", Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}

#nav {
	padding: 30px;
}

#nav a {
	font-weight: bold;
	color: #2c3e50;
}

#nav a.router-link-exact-active {
	color: #42b983;
}
</style>
