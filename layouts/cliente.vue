<template>
    <div>
        <h1>Layout Cliente</h1>
        <div> Hola {{nombre}} </div>
        <div>
            <Nuxt />
        </div>
    </div>
</template>
<script>
export default {
    beforeMount(){
        this.loadUser();
    },
    data(){
        return{
            nombre:""
        }
    },
    methods:{
        loadUser(){
            this.nombre = localStorage.getItem("nombre");
        }
    }
}
</script>