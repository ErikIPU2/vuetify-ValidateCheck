<template>
	<v-app :dark="dark">
	
		<detect-network v-on:detected-condition="offDet"></detect-network>
	
	
		<v-toolbar flat v-if="!isLoged">
			<v-spacer></v-spacer>
			<v-btn icon @click="invert_colors">
				<v-icon>mdi-invert-colors</v-icon>
			</v-btn>
		</v-toolbar>
	
		<v-toolbar v-else app>
			<v-btn icon>
				<v-icon>mdi-menu</v-icon>
			</v-btn>
			<v-toolbar-title>ValididateCheck</v-toolbar-title>
		</v-toolbar>
	
		<!-- TOOOLBARS -->
	
		<v-content v-if="!isLoged">
	
			<v-container v-if="loaded" fill-height fluid>
				<v-layout align-center justify-center row>
					<v-flex class="text-xs-center">
						<v-progress-circular :size="100" indeterminate></v-progress-circular>
						<h3>Caregando...</h3>
					</v-flex>
				</v-layout>
			</v-container>
	
			<v-container v-else-if="!online" fill-height fluid>
				<v-layout align-center justify-center row>
					<v-flex class="text-xs-center">
						<v-progress-circular :size="100" indeterminate></v-progress-circular>
						<h3>Conectando...</h3>
					</v-flex>
				</v-layout>
			</v-container>
	
			<v-conteiner v-else fill-height>
				<v-layout align-center justify-center row>
					<v-flex xs12 sm10 md8>
						<v-card>
							<v-toolbar color="primary">
								<v-toolbar-title class="white--text">Entrar</v-toolbar-title>
								<v-spacer></v-spacer>
	
								<v-btn @click="googleLogin" flat class="white--text">
									<v-icon>mdi-google</v-icon>
									<span class="hidden-xs-only">Logar com Google</span>
								</v-btn>
	
							</v-toolbar>
	
							<v-img :src="`public/card-img-${card_img}`" transition="slide-x-transition">
								<v-layout align-end justify-center row fill-height>
									<v-flex>
	
										<v-form v-model="login_valid" class="pa-3">
											<v-input :background-color="inputColor">
												<v-text-field v-model="emailInput" prepend-icon="mdi-account-circle" :rules="emailRules" label="Email" required></v-text-field>
											</v-input>
											<v-input :background-color="inputColor">
												<v-text-field v-model="passwordInput" prepend-icon="mdi-lock" :rules="passwordRules" type="password" label="Senha" required></v-text-field>
											</v-input>
	
											<v-layout align-center justify-end column fill-height>
												<v-flex>
													<v-btn large @click="login()">
														<v-icon>mdi-login</v-icon>
														Entrar
													</v-btn>
													<v-btn large @click="signUp()">
														Cadastrar
														<v-icon>mdi-account-plus</v-icon>
													</v-btn>
												</v-flex>
											</v-layout>
										</v-form>
	
									</v-flex>
								</v-layout>
							</v-img>
						</v-card>
					</v-flex>
				</v-layout>
			</v-conteiner>
	
		</v-content>
	
		<v-content v-else>
			<v-btn @click="logOut()">
				deslogar
			</v-btn>
		</v-content>
	
		<v-footer class="pa-3">
			<div>Erik Borella</div>
			<v-spacer></v-spacer>
			<div>&copy; {{ new Date().getFullYear() }}</div>
		</v-footer>
	</v-app>
</template>

<script>
	import detectNetwork from "v-offline";
	
	export default {
	
		components: {
			detectNetwork
		},
	
		created() {
			//serve para ver se já existe alguem logado
			firebase.auth().onAuthStateChanged(user => {
				this.user = user;
				if (user) {
					this.isLoged = true;
				} else {
					this.isLoged = false;
				}
				this.loaded = false;
			});
	
		},
		data() {
			return {
				dark: false,
				card_img: "day.jpg",
				login_valid: false,
	
				emailRules: [
					v => !!v || 'Email é necessario',
					v => /.+@.+/.test(v) || 'Email deve ser valido'
				],
				passwordRules: [
					v => !!v || "Digite a senha"
				],
	
				inputColor: "rgba(255,255,255,.4)",
	
				emailInput: "",
				passwordInput: "",
	
	
				isLoged: false,
				loaded: true,
				online: "",
	
				user: ""
			}
		},
	
		methods: {
			invert_colors() {
				this.dark = !this.dark
	
				if (this.dark) {
					this.card_img = "night.jpg"
					this.inputColor = "rgba(0,0,0, .6)"
				} else {
					this.card_img = "day.jpg"
					this.inputColor = "rgba(255,255,255,.4)"
				}
			},
	
			//logar com o google
			googleLogin() {
				var provider = new firebase.auth.GoogleAuthProvider();
				firebase.auth().signInWithRedirect(provider).catch(e => {
					alert(e.code);
				})
			},
	
			//sair
			logOut() {
				firebase.auth().signOut();
			},
	
			//cadastrar
			signUp() {
				if (this.login_valid) {
					firebase.auth().createUserWithEmailAndPassword(this.emailInput, this.passwordInput).catch(e => {
						alert(e.code);
					})
				}
			},
	
			//logar
			login() {
				if (this.login_valid) {
					firebase.auth().signInWithEmailAndPassword(this.emailInput, this.passwordInput).catch(e => {
						alert(e.code);
					})
				}
			},
	
			offDet(e) {
				this.online = e;
			},
	
	
		}
	}
</script>
