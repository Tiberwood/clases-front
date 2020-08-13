<template>
    <v-col cols="6">
        <v-row
            :align="'center'"
            :justify="'center'"
        >
            <form>
                <v-text-field
                    v-model="user.username"
                    :error-messages="usernameErrors"
                    :counter="10"
                    label="Name"
                    required
                    @input="$v.user.username.$touch()"
                    @blur="$v.user.username.$touch()"
                ></v-text-field>
                <v-text-field
                    v-model="user.password"
                    :error-messages="passwordErrors"
                    label="Password"
                    :type="show1 ? 'text' : 'password'"
                    required
                    @input="$v.user.password.$touch()"
                    @blur="$v.user.password.$touch()"
                ></v-text-field>
                <v-btn class="mr-4" @click="login()">Login</v-btn>
                <v-btn @click="clear">clear</v-btn>
            </form>
        </v-row>
    </v-col>
</template>

<script>
import { validationMixin } from 'vuelidate'
import { required, maxLength } from 'vuelidate/lib/validators'

export default { 
    mixins: [validationMixin],

    validations: {
        user: {
            username: { required, maxLength: maxLength(10) },
            password: { required },
        }
    },
    /* Declaramos nuestras variables */
    data: () => ({
        headers: {
            'Access-Control-Allow-Headers': 'origin',
            'Access-Control-Allow-Credentials': 'true',
        },
        show1: false,
        select: null,
        items: [
            'Item 1',
            'Item 2',
            'Item 3',
            'Item 4',
        ],
        checkbox: false,
        user: {
            username: '',
            password: '',
        },
    }),
    /* Utilizar data reactiva */
    computed: {
      usernameErrors () {
        const errors = []
        if (!this.$v.user.username.$dirty) return errors
        !this.$v.user.username.maxLength && errors.push('No sea laji')
        !this.$v.user.username.required && errors.push('Name is required.')
        return errors
      },
      passwordErrors () {
        const errors = []
        if (!this.$v.user.password.$dirty) return errors
        !this.$v.user.password.required && errors.push('Paasword is required')
        return errors
      },
    },
    /* Métodos */
    methods: {
        login: function() {
            this.$http.post('http://localhost:8000/auth/login/', this.user, this.headers)
                .then(response => {
                    if(response.status == 200){
                        localStorage.setItem('userKey', JSON.stringify(response.data));
                        this.$router.push('Home');
                    }
                    console.log('response', response);
                }).catch((error) =>{
                    console.error('error', error);
                });
        },
        submit () {
            this.$v.$touch()
        },
        clear () {
            this.$v.$reset()
            this.name = ''
            this.email = ''
            this.select = null
            this.checkbox = false
        },
    },
}
</script>

<style>

</style>