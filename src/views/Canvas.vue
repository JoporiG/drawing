<template>
  <div class="canvas-container">
      <canvas class="my-canvas" id="myCan" @click="CanvasClicked" width="600" height="600">

      </canvas>
      <button @click="DrawLine">Нарисовать линию

        </button>
  </div>
</template>

<style lang="scss">
    .my-canvas{
        width: 600px;
        background-color: lightgrey;
        height: 600px;
    }
    .canvas-container{
        width: 600px;
        height: 600px;
    }
</style>

<script>
export default {
    data(){
        return {
            canvas: null,
            points: [],
            P: [],
            Draw: "Point"
        }
    },
    methods:{
        CanvasClear(){
            const ctx = this.canvas.getContext("2d");
            ctx.fillStyle = "white";
            ctx.fillRect(0, 0, 600, 600)
            ctx.fill();
        },
        drawPoint(point){
            if(this.Draw === "Point"){
                const {x, y, color} = point
                const ctx = this.canvas.getContext("2d");
                ctx.fillStyle = color;
                const width = 5;
                const height = 5;
                ctx.fillRect(x - width / 2, y - height / 2, width, height);
                ctx.fill();
            } else if(this.Draw == "Line"){
                if(this.P.length == 2){
                    console.log(this.P);
                    this.P.length = 0;
                } else {
                    this.P.push(point);
                }
            }
        },
        CanvasClicked(event){
            this.points.push({x:event.pageX, y: event.pageY, color: "green"})
            this.$axios.get("http://188.225.47.187/api/canvas/setpoint.php", {
                params:{
                    x: event.pageX,
                    y: event.pageY
                }
            })
        },
        loadPoints(){
            this.$axios.get("http://188.225.47.187/api/canvas/points.php").then((response)=>{
                console.log(response)
                this.points = response.data.filter((el) => el.x !== null && el.y !== null)
            })
        },
        DrawLine(){
            if(this.Draw == "Line")
                this.Draw = "Point";
            else 
                this.Draw = "Line";
        }
    },
    mounted(){
        this.canvas = document.getElementById("myCan");
        /*setInterval(()=>{
            this.loadPoints();
        }, 4000)*/
    },
    watch:{
        points(){
            this.CanvasClear();
            for(const point of this.points){
                this.drawPoint(point);
            }
        }
    }
}
</script>