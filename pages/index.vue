<template>
  <div class="container">
    <div>



      <h1 class="title">
        実績解除ジェネレーター
      </h1>
      

        <div>

          <input
            ref="input"
            type="file"
            name="image"
            accept="image/*"
            @change="setImage"
          />

          <input
            ref="troinp"
            type="file"
            name="troinp"
            accdpt="image/*"
            @change="setImageTro"
            />

          <div id="img-container">
                <img ref="img" name="imgSrc" src="" width="980" key="imgSrc">
                <img ref="jimg" name="jimg" :src="jimg">
                <img ref="tro" name="tro" :src="tro">
            </div>
            <div>
                <canvas 
                  ref="mcv" 
                  name="mainCanvas" 
                  class="img-canvas" 
                  width="980" 
                  height="580"></canvas>
            </div>


            <div>
            <button type="button" class="btn btn-primary btn-lg" @click.prevent="showFileChooser">
            
            背景画像を変更

          </button>

          <button type="button" class="btn btn-primary btn-lg"
            @click.prevent="showTroFileChooser"
          >
            トロフィー画像を変更
          </button>
          </div>

          <div style="padding-top: 20px;">
            実績内容：<input type="text" ref="jisseki" @change="txtChange" :value="jisseki">
          </div>

          <div style="padding-top: 20px;">
            <button type="button" class="btn btn-success btn-lg" @click.prevent="saveCanvas">
            保存する
            </button>
            <a ref="download_link"></a>
          </div>

        </div>





      </div>
    </div>
  </div>
</template>

<script>
import JissekiBg from '~/assets/mes01.png';
import Basic980 from '~/assets/980x580.png';
import Trofie from '~/assets/undoukai_trophy_gold.png';


export default {
  components: {
    
  },
  data(){
    return {
      imgSrc: Basic980,
      jimg: JissekiBg,
      tro: Trofie,
      jisseki: 'ここになんかいれて'
    };

  },
  methods:{
    setImage(e) {
      const file = e.target.files[0];
      if (file.type.indexOf('image/') === -1) {
        alert('Please select an image file');
        return;
      }
      if (typeof FileReader === 'function') {
        const reader = new FileReader();
        reader.onload = (event) => {
        
          this.imgSrc = event.target.result;
          this.$forceUpdate();
          this.createCanvas();
          
        };
        reader.readAsDataURL(file);
      } else {
        alert('Sorry, FileReader API not supported');
      }
    },
    setImageTro(e) {
      const file = e.target.files[0];
      if (file.type.indexOf('image/') === -1) {
        alert('Please select an image file');
        return;
      }
      if (typeof FileReader === 'function') {
        const reader = new FileReader();
        reader.onload = (event) => {
        
          this.tro = event.target.result;
          this.createCanvas();
          
        };
        reader.readAsDataURL(file);
      } else {
        alert('Sorry, FileReader API not supported');
      }
    },
    showFileChooser(){
      this.$refs.input.click();
    },
    showTroFileChooser(){
      this.$refs.troinp.click();
    },
    createCanvas(){

      console.log(this);
      var img2src = this.jimgobj;
      var imgtro = this.tro;
      var txt_jisseki = this.$refs.jisseki;

      let canvasWidth = 980;
      let canvasHeight = 580;
      let ctx = this.$refs.mcv.getContext('2d');
      ctx.clearRect(0,0, canvasWidth, canvasHeight);
      let img = new Image();
      img.src = this.imgSrc;
      img.onload = function() {
      console.log(this);
        let cw = canvasWidth * (canvasHeight / canvasWidth );
        let cx = 0;
        if ( cw < canvasWidth ) {
          cx = (canvasWidth - cw) / 2;
        }
        var hRatio = canvasWidth  / img.width    ;
       var vRatio =  canvasHeight / img.height  ;
       var ratio  = Math.min ( hRatio, vRatio );
       var centerShift_x = ( canvasWidth - img.width*ratio ) / 2;
       var centerShift_y = ( canvasHeight - img.height*ratio ) / 2;  
       ctx.clearRect(0,0,canvasWidth, canvasHeight);
       ctx.drawImage(img, 0,0, img.width, img.height,
                  centerShift_x,centerShift_y,img.width*ratio, img.height*ratio); 
        

        //console.log(this.jimgobj);
        let image2 = new Image();
        image2.onload = function () {

         ctx.drawImage(image2,180,0);

         let image_tro = new Image();
         image_tro.src = imgtro;
         image_tro.onload = function () {
          ctx.drawImage(image_tro, 220, 20, 100, 100);

          console.log(txt_jisseki)
          ctx.font = "30px Arial bold";
          ctx.fillText("実績のロックを解除しました！", 340, 60);

          ctx.font = "20px Arial bold";
          ctx.fillText(txt_jisseki.value, 340, 100);



         }
       }
       image2.src = img2src;


       
      }
    },
    txtChange(e){
      console.log(e);
      this.createCanvas();
    },
    saveCanvas(){

      let canvas = this.$refs.mcv;
      let downloadLink = this.$refs.download_link;
      let filename = "download.png";
      console.log(canvas.msToBlob);
      if (canvas.msToBlob) {
            var blob = canvas.msToBlob();
            window.navigator.msSaveBlob(blob, filename);
        } else {
            downloadLink.href = canvas.toDataURL('image/png');
            downloadLink.download = filename;
            downloadLink.click();
        }


    }
  },
  mounted(){
    this.jimgobj = this.$refs.jimg.src;
    //console.log(this.jimgobj);
    this.createCanvas();
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  /* min-height: 100vh; */
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

input[type="file"] {
  display: none;
}
img {
  display: none;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 80px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
