<template>
    <div id="app">
        <h1>
            <img src="./assets/logo.svg" alt="Enroller" class="logo">
            System do zapisów na zajęcia
        </h1>
        <div v-if="authenticatedUsername">
            <h2>Witaj {{ authenticatedUsername }}!
                <a @click="logout()" class="float-right  button-outline button">Wyloguj</a>
            </h2>
            <meetings-page :username="authenticatedUsername"></meetings-page>
        </div>
        <div v-else>
            <button :class="registerForm ? 'button-outline' : ''" @click="swtichForLogin">Logowanie</button>
            <button :class="loginForm ? 'button-outline' : ''" @click="swtichForRegister">Rejestracja</button>
            <div v-if="error" class="error-alert">{{error}}</div>
            <div v-if="loginForm">
                <login-form @login="login($event)"></login-form>
            </div>
            <div v-if="registerForm">
                <register-form @register="register($event)"></register-form>
            </div>
        </div>
    </div>
</template>

<script>
    import "milligram";
    import LoginForm from "./LoginForm";
    import MeetingsPage from "./meetings/MeetingsPage";
    import RegisterForm from "./RegisterForm";

    export default {
        components: {RegisterForm, LoginForm, MeetingsPage},
        data() {
            return {
                authenticatedUsername: "", loginForm: true, registerForm: false, error: '',
            };
        },
        methods: {
            login(user) {
                this.authenticatedUsername = user.login;
            },
            logout() {
                this.authenticatedUsername = '';
            },
            register(user) {
                this.$http.post('participants', user)
                    .then(response => {
                        // udało się
                    })
                    .catch(response => {
                        this.error = 'Taki użytkownik już istnieje'// nie udało sie
                    });
            },
            swtichForLogin() {
                this.loginForm = true;
                this.registerForm = false;
            },
            swtichForRegister() {
                this.loginForm = false;
                this.registerForm = true;
            }
        }
    };
</script>

<style>
    #app {
        max-width: 1000px;
        margin: 0 auto;
    }

    .logo {
        vertical-align: middle;
    }
    .error-alert {
        border: 2px dotted red;
        background: pink;
        padding:  10px;
        text-align: center;
        border-radius: 5px;
        font-weight: bold;
    }
</style>

