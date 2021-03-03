<template>
    <div id="login">
        <input-box title="Вхід в FinansManager">
            <form @submit.prevent="$store.dispatch('login', {username: username, password: password})"
                  method="post">
                <label for="username">Ваш Логін</label>
                <b-form-input
                        id="username"
                        v-model="username"
                ></b-form-input>
                <label for="pasword">Ваш Пароль</label>
                <b-form-input
                        id="pasword"
                        v-model="password"
                        type="password"
                ></b-form-input>
                <button-component>Ввійти</button-component>
            </form>
        </input-box>
    </div>
</template>

<script>
    import InputBox from '@/components/InputBox.vue'
    import ButtonComponent from '@/components/ButtonComponent.vue'

    export default {
        name: "Login",
        data() {
            return {
                username: '',
                password: ''
            }
        },
        created() {
            this.$Progress.finish()
        },
        components: {ButtonComponent, InputBox}
    }
</script>

<style scoped lang="less">
    #login {
        background: #eeeeee;
        font-family: 'Open Sans', sans-serif;
    }
</style>