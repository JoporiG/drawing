<template>
  <div class="canvas-container">
      <canvas class="my-canvas" id="myCan" @click="CanvasClicked" width="600" height="600">

      </canvas> 
      <button @click="Change">Нарисовать линию</button> <br/>
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
            ctx.fillStyle = "lightgrey";
            ctx.fillRect(0, 0, 600, 600)
            ctx.fill();
        },
        drawPoint(point){
            const {x, y, color} = point
            const ctx = this.canvas.getContext("2d");
            ctx.fillStyle = color;
            const width = 5;
            const height = 5;
            ctx.fillRect(x - width / 2, y - height / 2, width, height);
            ctx.fill();
        },
        drawLine(){
                //this.CanvasClear();
                //const {x, y, color} = this.P[0]
                //const ctx = this.canvas.getContext("2d");
                //ctx.fillStyle = color;
                //const width = 5;
                //const height = 5;
                let dist = Math.sqrt(Math.pow(this.P[0].x - this.P[1].x, 2) + Math.pow(this.P[0].y - this.P[1].y, 2))
                let Xcoff = Math.abs(this.P[0].x - this.P[1].x) / dist;
                let Ycoff = Math.abs(this.P[0].y - this.P[1].y) / dist;
                if(this.P[0].y > this.P[1].y){
                    Ycoff *= -1;
                }
                if(this.P[0].x > this.P[1].x){
                    Xcoff *= -1;
                }
                for(let i = 0; i < dist; i += 4){
                    let PA = {
                        x: this.P[0].x + Xcoff * i,
                        y: this.P[0].y + Ycoff * i,
                    }
                    /*this.$axios.get("http://188.225.47.187/api/canvas/setpoint.php", {
                        params:{
                            x: PA.x,s
                            y: PA.y
                        }
                    })*/
                    this.points.push({x: PA.x, y: PA.y, color: "yellow"})
                    
                }
                //ctx.fillRect(x - width / 2, y - height / 2, width, height);
                //ctx.fill();

        },
        CanvasClicked(event){
            if(this.Draw == "Point"){
                this.points.push({x: event.pageX, y: event.pageY, color: "green"})
                this.$axios.get("http://188.225.47.187/api/canvas/setpoint.php", {
                    params:{
                        x: event.pageX,
                        y: event.pageY
                    }
                })
            } else if(this.Draw == "Line"){
                this.P.push({x: event.pageX, y: event.pageY});
                if(this.P.length == 2){
                    this.drawLine();
                    this.P.length = 0;
                }
            }
        },
        loadPoints(){
            this.$axios.get("http://188.225.47.187/api/canvas/points.php").then((response)=>{
                this.points = response.data.filter((el) => el.x !== null && el.y !== null)
            })
        },
        Change(){
            if(this.Draw == "Line")
                this.Draw = "Point";
            else 
                this.Draw = "Line";
        }
    },
    mounted(){
        this.canvas = document.getElementById("myCan");
        setInterval(()=>{
            this.loadPoints();
        }, 4000)
    },
    watch:{
        points(){
            //this.CanvasClear();
            for(const point of this.points){
                this.drawPoint(point);
            }
        }
    }
}
</script>