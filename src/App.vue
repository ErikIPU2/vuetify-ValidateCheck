<template>
	<v-app :dark="dark">
	
		<detect-network :detected-condition="offDet"></detect-network>
	
	
		<v-toolbar flat v-if="!isLoged">
			<v-spacer></v-spacer>
			<v-btn icon @click="invert_colors">
				<v-icon>mdi-invert-colors</v-icon>
			</v-btn>
		</v-toolbar>
	
		<v-toolbar v-else app clipped-left flat>
			<v-btn class="hidden-md-and-up" icon @click="sidenavShow = !sidenavShow">
				<v-icon>mdi-menu</v-icon>
			</v-btn>
			<v-toolbar-title>ValididateCheck</v-toolbar-title>
		</v-toolbar>
	
		<!-- TOOOLBARS -->
	
		<v-navigation-drawer :mini-variant="SideMini && ($vuetify.breakpoint.name !== 'xs' && $vuetify.breakpoint.name !== 'sm')" v-if="isLoged" :permanent="$vuetify.breakpoint.name !== 'xs' && $vuetify.breakpoint.name !== 'sm'" clipped app v-model="sidenavShow">
	
			<v-toolbar flat>
				<v-list class="pa-0">
					<v-list-tile avatar>
	
						<v-list-tile-avatar @click="SideMini = !SideMini">
							<img :src="user.photoURL">
						</v-list-tile-avatar>
	
						<v-list-tile-content>
							<v-list-tile-title>{{ user.displayName }}</v-list-tile-title>
						</v-list-tile-content>
	
						<v-list-tile-action>
							<v-btn icon @click="SideMini = !SideMini" class="hidden-sm-and-down">
								<v-icon>mdi-menu-left</v-icon>
							</v-btn>
						</v-list-tile-action>
					</v-list-tile>
				</v-list>
			</v-toolbar>
	
			<v-list class="pa-0" dense>
	
				<v-divider></v-divider>
				<v-subheader>Produtos</v-subheader>
	
				<v-list-tile>
					<v-list-tile-action>
						<v-btn icon>
							<v-icon>mdi-plus-circle-outline</v-icon>
						</v-btn>
					</v-list-tile-action>
	
					<v-list-tile-content>
						Adicionar Produto
					</v-list-tile-content>
				</v-list-tile>
	
			</v-list>
			
			<v-divider></v-divider>

			<v-subheader>Opções</v-subheader>
	
			<v-list class="pa-0" dense>
	
	
				<v-list-tile @click="invert_colors()">
					<v-list-tile-action>
						<v-btn icon>
							<v-icon>mdi-invert-colors</v-icon>
						</v-btn>
					</v-list-tile-action>
	
					<v-list-tile-content>
						Modo escuro
					</v-list-tile-content>
				</v-list-tile>
	
				<v-list-tile @click="logOut()">
					<v-list-tile-action>
						<v-btn icon>
							<v-icon>mdi-logout</v-icon>
						</v-btn>
					</v-list-tile-action>
	
					<v-list-tile-content>
						Sair
					</v-list-tile-content>
				</v-list-tile>
	
	
			</v-list>
	
		</v-navigation-drawer>
	
		<!-- Navigation -->
	
	
		<v-content v-if="!isLoged">
	
			<v-container v-if="loaded" fill-height fluid>
				<v-layout align-center justify-center row>
					<v-flex class="text-xs-center">
						<v-progress-circular :size="100" indeterminate></v-progress-circular>
						<h3>Caregando...</h3>
					</v-flex>
				</v-layout>
			</v-container>
	
			<v-container v-else-if="false" fill-height fluid>
				<v-layout align-center justify-center row>
					<v-flex class="text-xs-center">
						<v-progress-circular :size="100" indeterminate></v-progress-circular>
						<h3>Conectando...</h3>
					</v-flex>
				</v-layout>
			</v-container>
	
			<v-container v-else fill-height>
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
			</v-container>
	
		</v-content>
	
		<v-content v-else>
			<!-- <v-btn @click="logOut()">
								deslogar
							</v-btn> -->
	
			<v-container>
				<v-layout>
					<v-flex xs12>
						<v-btn @click="logOut()">
							deslogar
						</v-btn>
					</v-flex>
				</v-layout>
			</v-container>
	
	
		</v-content>
	
		<v-footer class="pa-3" app>
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
	
		firebase: {
			DBContent: firebase.database().ref("data")
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
	
				this.$bindAsArray('DBItens', firebase.database().ref("data").child(this.user.uid))
	
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
	
				user: "",
				DBItens: [],
	
				sidenavShow: true,
				SideMini: true,
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
				console.log(e);
				this.online = e;
			},
		},
	}
</script>
