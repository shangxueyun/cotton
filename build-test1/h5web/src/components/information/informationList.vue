<template>
   <div class="delete">
       <div class="slider">
          <div class="content" 
           @touchstart='touchStart'
           @touchmove='touchMove'
           @touchend='touchEnd'
           :style="deleteSlider"
          >
        <!-- 插槽中放具体项目中需要内容     -->  
          <slot></slot>
          </div>
          <div class="remove active" ref='remove' @click.stop="handleClick">
            删除
          </div>
      </div>
    
   </div>
</template>
<script type="text/ecmascript-6">
 export default {
   props:{
     propE: {
      type: Object,
      default: function () {
      }
    }
   },
   data() {
   return {
        startX:0,  //触摸位置
        endX:0,   //结束位置
        moveX: 0,  //滑动时的位置
        disX: 0,  //移动距离
        deleteSlider: '',//滑动时的效果,使用v-bind:style="deleteSlider"
      }
   },
 
   methods:{
     handleClick (v) {
         this.$emit('msg-from-child', this.val)
         this.deleteSlider = "transform:translateX(0px)";
     },
     touchStart(ev){
        // console.log(ev)
        ev= ev || event
     //tounches类数组，等于1时表示此时有只有一只手指在触摸屏幕
      if(ev.touches.length == 1){
          // 记录开始位置
          this.startX = ev.touches[0].clientX;
        }
      },
     touchMove(ev){
        ev = ev || event;
          //获取删除按钮的宽度，此宽度为滑块左滑的最大距离
        let wd=this.$refs.remove.offsetWidth;
          if(ev.touches.length == 1) {
            // 滑动时距离浏览器左侧实时距离
            this.moveX = ev.touches[0].clientX
        
            //起始位置减去 实时的滑动的距离，得到手指实时偏移距离
            this.disX = this.startX - this.moveX;
            // 如果是向右滑动或者不滑动，不改变滑块的位置
            if(this.disX < 0 || this.disX == 0) {
              this.deleteSlider = "transform:translateX(0px)";
            // 大于0，表示左滑了，此时滑块开始滑动 
            }else if (this.disX > 0) {
               //具体滑动距离我取的是 手指偏移距离*5。
              this.deleteSlider = "transform:translateX(-" + this.disX*5 + "px)";
              
              // 最大也只能等于删除按钮宽度 
              if (this.disX*5 >=wd) {
                this.deleteSlider = "transform:translateX(-" +wd+ "px)";
               
              }
            }
          }
       },
     touchEnd(ev){
       ev = ev || event;
       // console.log(ev)
       let wd=this.$refs.remove.offsetWidth;
       if (ev.changedTouches.length == 1) {
          let endX = ev.changedTouches[0].clientX;
           
            this.disX = this.startX - endX;
            //如果距离小于删除按钮一半,强行回到起点
            
            if ((this.disX*5) < (wd/2)) {
             
              this.deleteSlider = "transform:translateX(0px)";
            }else{
              //大于一半 滑动到最大值
               this.deleteSlider = "transform:translateX(-"+wd+ "px)";
            }
          }
        }   
   }
   }
</script>
<style lang="scss">
  .slider{
    width: 100%;
    height:1.46rem;
	border-bottom: 1px solid #f0f0f0;
    position: relative;
     user-select: none;
	 overflow: hidden;
    .content{
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      z-index: 100;
      //  设置过渡动画
      transition: 0.3s;
       
    }
    .remove{
      position: absolute;
      width:1.15rem;
      height:1.43rem;
      background:red;
      right: 0;
      top: 0.01rem;
	  bottom: 0;
      color:#fff;
      text-align: center;
      font-size: 0.3rem;
      line-height: 1.46rem;
    }
  }
 
</style>