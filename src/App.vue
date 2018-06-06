<template>
  <div id="app" class="container">
    <input type="file" class="file">
    <b-row>
      <b-col>
        <div class="histogram histogramR"></div>
  
      </b-col>
      <b-col>
        <div class="histogram histogramG"></div>
  
      </b-col>
      <b-col>
        <div class="histogram histogramB"></div>
  
      </b-col>
    </b-row>
  
  
  </div>
</template>

<script>
  export default {
    name: 'app',
    data() {
      return {
        msg: 'Welcome to Your Vue.js App'
      }
    },
  
  
    methods: {
      getCanvasFromFile(file) {
        let canvas = document.createElement('canvas');
        console.log(canvas.getContext('2d'));
        let context = canvas.getContext('2d');
        let image = new Image();
        image.src = URL.createObjectURL(file);
        return new Promise((resolve) => {
          image.onload = function() {
            canvas.height = image.height;
            canvas.width = image.width;
            context.drawImage(image, 0, 0);
            URL.revokeObjectURL(file);
            resolve(context.getImageData(0, 0, image.width, image.height).data);
            let oldImage = document.body.querySelector('img');
            if (oldImage) {
              document.body.removeChild(oldImage);
            }
            document.body.insertBefore(image, document.body.firstChild);
          };
        });
      },
  
      processImageHistogram(imageDataArray) {
        var i = 0;
        var histogramR = new Array(256).fill(0);
        var histogramG = new Array(256).fill(0);
        var histogramB = new Array(256).fill(0);
        var pixelCount = imageDataArray.length / 4;
        while (i < imageDataArray.length) {
          var r = imageDataArray[i++];
          var g = imageDataArray[i++];
          var b = imageDataArray[i++];
          var a = imageDataArray[i++];
          histogramR[r]++;
          histogramG[g]++;
          histogramB[b]++;
        }
        return {
          histogramR,
          histogramG,
          histogramB,
          pixelCount
        };
      },
  
      drawHistograms(histograms) {
        var histogramR = histograms.histogramR;
        var histogramG = histograms.histogramG;
        var histogramB = histograms.histogramB;
        var pixelCount = histograms.pixelCount;
        var histogramRElement = document.querySelector('.histogramR');
        var histogramGElement = document.querySelector('.histogramG');
        var histogramBElement = document.querySelector('.histogramB');
        histogramRElement.innerHTML = '';
        histogramGElement.innerHTML = '';
        histogramBElement.innerHTML = '';
        this.drawHistogram(histogramR, histogramRElement);
        this.drawHistogram(histogramG, histogramGElement);
        this.drawHistogram(histogramB, histogramBElement);
      },
  
      drawHistogram(histogram, element) {
        var max = Math.max.apply(Math, histogram);
        for (var i = 0; i < histogram.length; i++) {
          var div = document.createElement('div');
          div.classList.add('histogram-slice');
          var height = histogram[i] / max * 100;
          div.style = `height: ${height}%`;
          element.appendChild(div);
        }
      },
    },
    mounted() {
      var fileElement = document.querySelector('.file');
      // console.log(document);
      fileElement.addEventListener('change', (e) => {
        this.getCanvasFromFile(fileElement.files[0])
          .then(this.processImageHistogram)
          .then(this.drawHistograms);
      });
    }
  }
</script>

<style lang="scss">
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
    img {
    width: 200px;
  }
  h1,
  h2 {
    font-weight: normal;
  }
  
  ul {
    list-style-type: none;
    padding: 0;
  }
  
  li {
    display: inline-block;
    margin: 0 10px;
  }
  
  a {
    color: #42b983;
  }
  
  .histogram-slice {
    width: 1px;
  }
  
  .histogram {
    display: flex;
    height: 200px;
    width: 256px;
    margin-bottom: 10px;
    border: 1px solid gray;
    align-items: flex-end;
  }
  
  .histogramR .histogram-slice {
    background-color: red;
  }
  
  .histogramG .histogram-slice {
    background-color: green;
  }
  
  .histogramB .histogram-slice {
    background-color: blue;
  }
</style>
