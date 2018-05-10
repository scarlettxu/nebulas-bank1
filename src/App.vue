<template>
  <div id="app">
    <div v-show="!showBank" class="ground">
      <img src="./assets/logo.png">
      <p id="idea">
      </p>
      <Button class="button" type="success" long @click="goto" v-show="showButton">去往纳不勒斯银行</Button>
    </div>

    <FinancialAssistant v-show="showBank" @back="back"/>
    <canvas id="canvas" width="100%" height="100%"></canvas>
  </div>
</template>

<script>
import FinancialAssistant from './components/FinancialAssistant'

export default {
  name: 'App',
  data(){
    return{
      showBank: true,
      showButton: false,
    }
  },
  components: {
    FinancialAssistant
  },
  mounted(){
    this.showWords();

    console.log('vue获取dom元素')
    this.$nextTick(function () {
      this.drawCanvas();
    })

  },
  methods: {
    goto: function () {
      this.showBank = true;
    },
    showWords: function () {
      var $this = this;
      var index=0;
      var str = `2018年5月7日，小砺第一次踏上了纳不勒斯星球<br/>呼吸着比地球更清新的空气，仰望着蔚蓝的星空，小砺暗下决心，我要留在这里...
        <br>我要存很多很多纳不勒斯币😄
        <br>我要买一座大大的纳不勒斯太空堡😄
        <br>首先，我要去纳不勒斯银行算算我的资金和需要的贷款😢`;
      function print() {
        if(index == str.length+1){
          clearInterval(timer);
          $this.showButton = true;
        } else {
          document.querySelector('#idea').innerHTML=str.substring(0,index++);
        }
      }
      var timer = setInterval(print,200);
    },
    back: function () {
      this.showBank = false;
    },
    drawCanvas: function () {
      /*
       * @var star_r：star半径系数，系数越大，半径越大
       * @var star_alpha：生成star的透明度，star_alpha越大，透明度越低
       * @var initStarsPopulation：初始化stars的个数
       * @var move_distance：star位移的距离，数值越大，位移越大
       * @var dot_r : dot半径系数，系数越大，半径越大
       * @var dot_speeds : dots运动的速度
       * @var dot_alpha : dots的透明度
       * @var aReduction：dot消失条件，透明度小于aReduction时消失
       * @var dotsMinDist：dot最小距离
       * @var maxDistFromCursor：dot最大距离
       *
       * */
      var config = {
        star_r : 3,
        star_alpha : 5,
        initStarsPopulation : 150,
        move_distance : 0.25,
        dot_r : 5,
        dot_speeds : 0.5,
        dot_alpha : 0.5,
        dot_aReduction : 0.01,
        dotsMinDist : 5,
        maxDistFromCursor : 50,
      };
      var stars = [],
        dots = [],
        canvas = document.getElementById('canvas'),
        ctx = canvas.getContext('2d'),
        WIDTH,
        HEIGHT,
        mouseMoving = false,
        mouseMoveChecker,
        mouseX,
        mouseY;
      function CanvasStar(){}

      var initConfig = function(conf){
        if( conf instanceof Object )
          for( var item in conf ){
            config[item] = conf[item];
          }
      };

      CanvasStar.prototype.init =function (conf) {
        initConfig(conf);//初始化设置

        ctx.strokeStyle = "white";
        ctx.shadowColor = "white";
        for (var i = 0; i < config.initStarsPopulation; i++) {
          stars[i] = new Star(i, Math.floor(Math.random()*WIDTH), Math.floor(Math.random()*HEIGHT),true);
          //stars[i].draw();
        }
        ctx.shadowBlur = 0;
        animate();
      };



      function Star(id, x, y, useCache) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.useCacha = useCache;
        this.cacheCanvas = document.createElement("canvas");
        this.cacheCtx = this.cacheCanvas.getContext("2d");
        this.r = Math.floor(Math.random() * config.star_r) + 1;
        this.cacheCtx.width = 6 * this.r;
        this.cacheCtx.height = 6 * this.r;
        var alpha = ( Math.floor(Math.random() * 10) + 1) / config.star_alpha;
        this.color = "rgba(255,255,255," + alpha + ")";
        if (useCache) {
          this.cache()
        }
      }

      Star.prototype = {
        draw : function () {
          if (!this.useCacha) {
            ctx.save();
            ctx.fillStyle = this.color;
            ctx.shadowBlur = this.r * 2;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
            ctx.closePath();
            ctx.fill();
            ctx.restore();
          } else {
            ctx.drawImage(this.cacheCanvas, this.x - this.r, this.y - this.r);
          }
        },

        cache : function () {
          this.cacheCtx.save();
          this.cacheCtx.fillStyle = this.color;
          this.cacheCtx.shadowColor = "white";
          this.cacheCtx.shadowBlur = this.r * 2;
          this.cacheCtx.beginPath();
          this.cacheCtx.arc(this.r * 3, this.r * 3, this.r, 0, 2 * Math.PI);
          this.cacheCtx.closePath();
          this.cacheCtx.fill();
          this.cacheCtx.restore();
        },

        move : function () {
          this.y -= config.move_distance;
          if (this.y <= -10) {
            this.y += HEIGHT + 10;
          }
          this.draw();
        },

        die : function () {
          stars[this.id] = null;
          delete stars[this.id]
        }
      };

      function Dot(id, x, y, useCache) {
        this.id = id;
        this.x = x;
        this.y = y;
        this.r = Math.floor(Math.random() * config.dot_r)+1;
        this.speed = config.dot_speeds;
        this.a = config.dot_alpha;
        this.aReduction = config.dot_aReduction;
        this.useCache = useCache;
        this.dotCanvas = document.createElement("canvas");
        this.dotCtx = this.dotCanvas.getContext("2d");
        this.dotCtx.width = 6 * this.r;
        this.dotCtx.height = 6 * this.r;
        this.dotCtx.a = 0.5;
        this.color = "rgba(255,255,255," + this.a +")";
        this.dotCtx.color = "rgba(255,255,255," + this.dotCtx.a + ")";
        this.linkColor = "rgba(255,255,255," + this.a/4 + ")";
        this.dir = Math.floor(Math.random()*140)+200;

        if( useCache){
          this.cache()
        }
      }

      Dot.prototype = {
        draw : function () {
          if( !this.useCache){
            ctx.save();
            ctx.fillStyle = this.color;
            ctx.shadowColor = "white";
            ctx.shadowBlur = this.r * 2;
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
            ctx.closePath();
            ctx.fill();
            ctx.restore();
          }else{
            ctx.drawImage(this.dotCanvas, this.x - this.r * 3, this.y - this.r *3);

          }
        },

        cache : function () {
          this.dotCtx.save();
          this.dotCtx.a  -= this.aReduction;
          this.dotCtx.color = "rgba(255,255,255," + this.dotCtx.a + ")";
          this.dotCtx.fillStyle = this.dotCtx.color;
          this.dotCtx.shadowColor = "white";
          this.dotCtx.shadowBlur = this.r * 2;
          this.dotCtx.beginPath();
          this.dotCtx.arc(this.r * 3, this.r * 3, this.r, 0, 2 * Math.PI, false);
          this.dotCtx.closePath();
          this.dotCtx.fill();
          this.dotCtx.restore();
        },
        link : function () {
          if (this.id == 0) return;
          var previousDot1 = getPreviousDot(this.id, 1);
          var previousDot2 = getPreviousDot(this.id, 2);
          var previousDot3 = getPreviousDot(this.id, 3);
          var previousDot4 = getPreviousDot(this.id, 4);


          if (!previousDot1) return;
          ctx.strokeStyle = this.linkColor;
          ctx.moveTo(previousDot1.x, previousDot1.y);
          ctx.beginPath();
          ctx.lineTo(this.x, this.y);
          if (previousDot2 != false) ctx.lineTo(previousDot2.x, previousDot2.y);
          if (previousDot3 != false) ctx.lineTo(previousDot3.x, previousDot3.y);
          if (previousDot4 != false) ctx.lineTo(previousDot4.x, previousDot4.y);

          ctx.stroke();
          ctx.closePath();
        },

        move : function () {


          this.a -= this.aReduction;
          if(this.a <= 0 ){
            this.die();
            return
          }
          this.dotCtx.a  -= this.aReduction;
          this.dotCtx.color = "rgba(255,255,255," + this.dotCtx.a + ")";
          this.color = "rgba(255,255,255," + this.a + ")";
          this.linkColor = "rgba(255,255,255," + this.a/4 + ")";
          this.x = this.x + Math.cos(degToRad(this.dir)) * this.speed;
          this.y = this.y + Math.sin(degToRad(this.dir)) * this.speed;

          this.draw();
          this.link();

        },

        die : function () {
          dots[this.id] = null;
          delete dots[this.id];
        }
      };

      window.onmousemove = function (e) {
        mouseMoving = true;
        mouseX = e.clientX;
        mouseY = e.clientY;
        clearInterval(mouseMoveChecker);
        mouseMoveChecker = setInterval(function () {
          mouseMoving = false
        },1000)

      };

      function drawIfMouseMoving() {
        if (!mouseMoving) return;

        if (dots.length == 0) {
          dots[0] = new Dot(0, mouseX, mouseY,true);
          dots[0].draw();
          return;
        }

        var previousDot = getPreviousDot(dots.length, 1);
        var prevX = previousDot.x;
        var prevY = previousDot.y;

        var diffX = Math.abs(prevX - mouseX);
        var diffY = Math.abs(prevY - mouseY);

        if (diffX < config.dotsMinDist || diffY < config.dotsMinDist) return;

        var xVariation = Math.random() > .5 ? -1 : 1;
        xVariation = xVariation*Math.floor(Math.random() * config.maxDistFromCursor)+1;
        var yVariation = Math.random() > .5 ? -1 : 1;
        yVariation = yVariation*Math.floor(Math.random() * config.maxDistFromCursor)+1;
        dots[dots.length] = new Dot(dots.length, mouseX+xVariation, mouseY+yVariation,true);
        dots[dots.length-1].draw();
        dots[dots.length-1].link();
      }

      function getPreviousDot(id, stepback) {
        if(id == 0 || id - stepback < 0){
          return false
        }
        if(typeof dots[id - stepback] !== "undefined"){
          return dots[id - stepback]
        }else{
          return false
        }
      }

      function setCanvasSize() {
        WIDTH = document.documentElement.clientWidth;
        HEIGHT = document.documentElement.clientHeight;
        canvas.setAttribute("width", WIDTH);
        canvas.setAttribute("height", HEIGHT);

      }

      function animate() {
        ctx.clearRect(0, 0, WIDTH, HEIGHT);

        for (var i in stars) {
          stars[i].move();
        }
        for (var i in dots) {
          dots[i].move();
        }
        drawIfMouseMoving();
        requestAnimationFrame(animate);
      }

      function degToRad(deg) {
        return deg * (Math.PI / 180);
      }

      setCanvasSize();

      var CanvasStar = new CanvasStar;
      CanvasStar.init();
    }
  }
}
</script>

<style>

  html, body, div, span, applet, object, iframe,
  h1, h2, h3, h4, h5, h6, p, blockquote, pre,
  a, abbr, acronym, address, big, cite, code,
  del, dfn, em, img, ins, kbd, q, s, samp,
  small, strike, strong, sub, sup, tt, var,
  b, u, i, center,
  dl, dt, dd, ol, ul, li,
  fieldset, form, label, legend,
  table, caption, tbody, tfoot, thead, tr, th, td,
  article, aside, canvas, details, embed,
  figure, figcaption, footer, header, hgroup,
  menu, nav, output, ruby, section, summary,
  time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font: inherit;
    vertical-align: baseline;
  }

  html,body{
    width: 100%;
    height: 100%;
    font-family: "Microsoft Yahei", "Arial Narrow", sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: left;
  }

  /* HTML5 display-role reset for older browsers */
  article, aside, details, figcaption, figure,
  footer, header, hgroup, menu, nav, section {
    display: block;
  }
  body {
    overflow: hidden;
  }
  ol, ul {
    list-style: none;
  }
  blockquote, q {
    quotes: none;
  }
  blockquote:before, blockquote:after,
  q:before, q:after {
    content: '';
    content: none;
  }
  table {
    border-collapse: collapse;
    border-spacing: 0;
  }

  @keyframes rolling {
    0%,100%{
      opacity: 0.9;
    }
    50%{
      opacity: 0.6;
    }
  }
#app {
  position: relative;
  width: 100%;
  height: 100%;
  /*background: #7bbfea; !* 一些不支持背景渐变的浏览器 *!*/
  background:linear-gradient(to bottom, #7bbfea, #00BFFF, #1A4FA3);
  background:-moz-linear-gradient(top, #7bbfea, #00BFFF, #1A4FA3);
  background:-webkit-linear-gradient(top, #7bbfea, #00BFFF, #1A4FA3);
  background:-o-linear-gradient(top, #7bbfea, #00BFFF, #1A4FA3);
  animation: rolling 2s infinite;
  -webkit-animation: rolling 2s ease-in-out infinite;
}
  .ground{
    width: 100%;
    height: 100%;
  }
  .ground>img{
    position: absolute;
    width: 4.5rem;
    height: 3.5rem;
    margin: 0 auto;
    left: 0;
    right: 0;
  }

  .ground>p{
    position: absolute;
    top: 4rem;
    left:0;
    right:0;
    width: 6.5rem;
    margin: 0 auto;
    font-size: .35rem;
    line-height: .68rem;
  }
  .button{
    position: absolute;
    bottom: 1rem;
    width: 50%;
    margin: 0 auto;
    left: 0;
    right: 0;
    font-size: .3rem;
    z-index: 999;
  }
  #canvas{
    position: absolute;
    top: 0;
    z-index: -1;
  }
</style>
