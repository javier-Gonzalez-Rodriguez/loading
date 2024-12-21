<template>
    <div class="loading-page" id="CustomLoading">
        <div class="loading-container1">
            <div class="loading-animacion-content1">
                <div class="element-color1">

                </div>
            </div>
        </div>

        <div class="loading-container2">
            <div class="loading-animacion-content2">
                <div class="element-color2">

                </div>
            </div>
        </div>

        <div class="loading-container3">
            <div class="loading-animacion-content3">
                <div class="element-color3">

                </div>
            </div>
        </div>

        <div class="loading-percent">
            <span>{{ porcentaje }}%</span>
        </div>
    </div>
</template>

<script>
    export default {
        name: "CustomLoading",
        props: {
            //valor en segundos
            estimatedTime: {
                type: Number,
                default: 120
            },
            referenceType: {//Opciones: ""time"" para dar un tiempo estimado hasta llegar al 100% o "manual" para aumentar el porcentaje manualmente
                type: String,
                default: 'time'
            }
        },  
        data() {
            return {
                porcentaje: 0,
                elapsedTime: 0,
                nextLoadingTick: 0,
            };
        },
        created() {
            if(this.referenceType == 'time'){
                this.nextLoadingTick = this.generarEsperaAleatoria();

                this.startLoading();
            }
        },
        mounted(){
            this.$emit('on-mounted', {'instance':this});
        },
        methods: {
            /**
             * inicia el intervalo aleatorio de incremento del porcentaje hasta el tiempo objetivo 
             * para llegar al porcentaje de carga del 100%
             */
            startLoading() {
                //se genera el intervalo que aumenta el porcentaje de carga
                const interval = setInterval(() => {
                    //se obtiene el tiempo pasado en segundos  hasta la ejecucion de este intervalo
                    this.elapsedTime += this.nextLoadingTick / 100;
                    //se calcula el porcentaje de carga usando el tiempo pasado con respecto el tiempo estimado
                    var aux = parseInt((this.elapsedTime / this.estimatedTime) * 100);

                    //si el resultado es mayor que 100 el porcentaje queda fijo a 100%
                    if(aux > 100){
                        this.porcentaje = 100;
                        this.porcenajeMaximo();
                    }else{//de lo contrario se asigna el % calculado
                        this.porcentaje = aux;
                    }

                    //se genera un tiempo de espera aleatorio hasta el proximo incremento del porcentaje de carga
                    this.nextLoadingTick = this.generarEsperaAleatoria();
                    //se eleimina el intervalo actual
                    clearInterval(interval);
                    //si el porcentaje no ha lleado a 100% se crea un nuevo intervalo uando el nuevo valor de nextLoadingTick
                    if(this.porcentaje < 100){
                        this.startLoading();
                    }
                    
                }, this.nextLoadingTick);
            },
            /**
             * genera un tiempo de espera aleatorio en segundos
             */
            generarEsperaAleatoria(){
                let max = 1000;
                let saltos = 100;

                // Calcular la cantidad de posibles valores (0, 100, 200, ..., 1000)
                let numeroDeValores = max / saltos;

                // Generar un número aleatorio entre 0 y numeroDeValores
                let aleatorio = Math.floor(Math.random() * (numeroDeValores + 1));
                
                // Multiplicar por el salto (100)
                return aleatorio * saltos;
            },
            /**
             * emite un evento para notificar que se ha llegado al 100% de porcentaje
             */
            porcenajeMaximo(){
                if(this.porcenaje == 100){
                    this.$emit('finished');
                }
            },
            /**
             * incrementa el porcentaje de forma manual
             * @param increment cantidad de porcentaje a aumentar
             */
            increasePercent(increment){
                this.porcentaje += increment;
                this.porcenajeMaximo();
            }
        },
    }
</script>

<style scoped>
    .loading-page{
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        background: linear-gradient(135deg, #4a4a4a7a, rgba(210, 210, 210, 0.478)); /* Fondo semi-transparente */
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 999999; /* Para asegurar que esté por encima de otros elementos */
    }

    .loading-container1{
        position: absolute;
        background-color: rgba(128, 128, 128, 0); 
        border: solid 2px black;
        border-radius: 50%;
        max-width: 200px;
        max-height: 200px;
        min-width: 50px;
        min-height: 50px;
        width: inherit;
        height: inherit;
        display: flex;
        justify-content: center;
        align-items: flex-end;
        transform-origin: 50% 50%;
        animation: rotacion 2s infinite linear;
        z-index: 1;
    }

    .loading-animacion-content1 {
        position: relative;
        height: 50%;
        min-width: 5%;
        display: flex;
        flex-direction: column-reverse;
    }

    .element-color1{
        background-color: aquamarine;
        width: 20px;
        height: 20px;
        border-radius: 50%;
    }

    .loading-container2{
        position: absolute;
        background-color: rgba(0, 0, 0, 0); 
        border: solid 2px black;
        border-radius: 50%;
        max-width: 150px;
        max-height: 150px;
        min-width: 50px;
        min-height: 45px;
        width: inherit;
        height: inherit;
        display: flex;
        justify-content: center;
        align-items: flex-end;
        transform-origin: 50% 50%;
        z-index: 2;
        animation: rotacion 1.5s infinite linear;
    }

    .loading-animacion-content2 {
        position: relative;
        height: 50%;
        min-width: 5%;
        display: flex;
        flex-direction: column-reverse;
    }

    .element-color2{
        background-color: rgb(217, 127, 255);
        width: 20px;
        height: 20px;
        border-radius: 50%;
    }

    .loading-container3{
        position: absolute;
        background-color: rgba(0, 0, 0, 0); 
        border: solid 2px black;
        border-radius: 50%;
        max-width: 100px;
        max-height: 100px;
        min-width: 50px;
        min-height: 40px;
        width: inherit;
        height: inherit;
        display: flex;
        justify-content: center;
        align-items: flex-end;
        transform-origin: 50% 50%;
        z-index: 3;
        animation: rotacion 1s infinite linear;
    }

    .loading-animacion-content3 {
        position: relative;
        height: 50%;
        min-width: 5%;
        display: flex;
        flex-direction: column-reverse;
    }

    
    .element-color3{
        background-color: rgb(127, 187, 255);
        width: 20px;
        height: 20px;
        border-radius: 50%;
    }

    @keyframes rotacion {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>