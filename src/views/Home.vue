<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <div class="drop-item" v-for="(item, index) in arr" :key="index" @dragstart="dragstart($event, index)" v-on:drag="drag" v-on:dragend="dragend"  :data-index="index">
      <img :src="item.src" :class="['img' +item.cla]">
      <div class="holder" v-if="item.showPlaceHolder"></div>
      <!-- <div class="drop-item-placeHolder" v-show="item.showPlaceHolder"></div> -->
    </div>
  </div>
</template>


<style lang="scss" scoped>
.home {
  position: absolute;
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
  .drop-item {
    position: relative;
    background: purple;
    cursor: pointer;
    -webkit-user-select:none;
    transition: all 2s;
    .img0 {
      height: 100px;
      width: 200px;
      vertical-align: middle;
      // transition: all 0.5s;
    }
    .img1 {
      height: 300px;
      width: 200px;
      vertical-align: middle;
      // transition: all 0.5s;
    }
    .img2 {
      height: 200px;
      width: 200px;
      vertical-align: middle;
      // transition: all 0.5s;
    }
    .img3 {
      height: 100px;
      width: 200px;
      vertical-align: middle;
      // transition: all 0.5s;
    }
    .holder {
      width: 100%;
      height: 100%;
      background: #fff;
      position: absolute;
      left: 0;
      top: 0;
    }

    // .img {
    //   font-size: 20px;
    //   text-align: center;
    //   height: 200px;
    //   width: 200px;
    //   vertical-align: middle;
    // }
  }
}
</style>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'home',
  data(){
    return {
      arr:[{
        src: 'https://hbimg.huabanimg.com/ae583a1c213c51012bb836495637e239a7e9590c253c8-ShpcpE_sq320',
        title: '生活馆',
        showPlaceHolder: false,
        id: 123,
        cla: 0
      }, {
        src: 'https://imglf6.ph.126.net/r6Oa30BUiJaANM2QgiuAWQ==/6597833927237755956.jpg',
        title: 'SPA馆',
        showPlaceHolder: false,
        id: 125,
        cla: 1
      }, {
        src: 'https://hbimg.huabanimg.com/c0aa4794b6f845f74780d7e6df29abeb8ec77b383e605-1iNrsx_sq320',
        title: '吃饭馆',
        showPlaceHolder: false,
        id: 127,
        cla: 2
      }, {
        src: 'https://hbimg.huabanimg.com/28d7bf3222aa7b6c1f4c461bb0e06542b385529f894c-iDvL4O_sq320',
        title: '休闲馆',
        showPlaceHolder: false,
        id: 129,
        cla: 3
      }],
      canMoveDowm: true,
      canMoveUp: true,
      startIndex: 0,
      moveIndex: 0,
      distanceY: 0,
      endIndex: 0,
      startY: 0,
      moveY: 0,
      endY: 0,
      srcElHeight: 0,
      needMoveHeight: 0,
      needMoveHeightUp: 0,
      dragEL: null,
      dragParentEl: null,
      nextEl: null,
      previousEl: null
    }
  },
  components: {
    HelloWorld
  },
  mounted() {

  },
  methods: {
    dragstart(e, index) {
      const dragEL = e.target
      this.dragParentEl = dragEL.parentElement
      this.nextEl = dragEL.parentElement
      this.previousEl = dragEL.parentElement
      // this.dragEL = dragEL
      this.startIndex = index
      this.startY = e.pageY
      this.srcElHeight = dragEL.clientHeight
      Array.from(document.getElementsByClassName('drop-item')).forEach(item => {
        item.style.transition = `all 0.3s`
      })
    },
    drag(e) {
      const dragEL = e.target
      const moveY = e.pageY - this.startY
      if(!this.arr[this.startIndex].showPlaceHolder) {
        this.arr[this.startIndex].showPlaceHolder = true
      }

      if(this.distanceY < e.pageY) {
        this.canMoveDowm = true
        this.canMoveUp = false        
      } else if(this.distanceY > e.pageY) {
        this.canMoveUp = true
        this.canMoveDowm = false
      } else {
        // console.log('静止不动')
      }
      this.distanceY = e.pageY
      
      // 向下移动换位置
      if(moveY >= this.needMoveHeight + (this.srcElHeight / 2) && this.canMoveDowm) {
        this.canMoveDowm = false
        let nextEl = this.nextEl.nextElementSibling
        if(!nextEl) {
          return
        }
        this.moveIndex++
        this._findNextEl()
      }
      // 向上移动换位置
      if(moveY <= (this.needMoveHeight - (this.srcElHeight / 2)) && this.canMoveUp && moveY != -this.startY) {
        this.canMoveUp = false
        let previousEl = this.previousEl.previousElementSibling
        if(!previousEl) {
          return
        }
        this.moveIndex--
        this._findPreviousEl()
      }
      // 向下移动换位置
    },
    dragend(e) {
      let arr = this.arr
      arr.forEach(item => {
        item.showPlaceHolder = false
      })
      const moveIndex = this.startIndex + this.moveIndex
      const tem = arr[this.startIndex]
      arr.splice(this.startIndex, 1)
      arr.splice(moveIndex , 0, tem)


      this.arr = arr
      this.$forceUpdate()
      Array.from(document.getElementsByClassName('drop-item')).forEach(item => {
        item.style.transition = `none`
        item.style.transform = `translateY(0px)`
      })
      this.canMoveDowm = true
      this.canMoveUp = true
      this.needMoveHeightUp = 0
      this.startIndex = 0
      this.moveIndex = 0
      this.endIndex = 0
      this.startY = 0
      this.distanceY = 0
      this.moveY = 0
      this.endY = 0
      this.srcElHeight = 0
      this.needMoveHeight = 0
      this.dragEL = null
      this.dragParentEl = null
      this.nextEl = null
      this.previousEl = null;
    },
    _findNextEl() {
      const el = this.nextEl
      let nextEl
      if(el && el.nextElementSibling) {
        nextEl = el.nextElementSibling
        this.nextEl = nextEl
        this.previousEl = nextEl
        if(this.moveIndex <= 0) {
          el.style.transform = `translateY(0px)`
          this.moveY = this.moveY + el.clientHeight
          this.needMoveHeight = el.clientHeight + this.needMoveHeight
        } else {
          this.moveY = this.moveY + nextEl.clientHeight
          nextEl.style.transform = `translateY(${-this.srcElHeight}px)`
          this.needMoveHeight = nextEl.clientHeight + this.needMoveHeight
        }
        this.dragParentEl.style.transform = `translateY(${this.moveY}px)`
        
        this.canMoveDowm = true
      } else {
        this.canMoveDowm = false
        return
      }
    },
    _findPreviousEl() {
      const el = this.previousEl
      let previousEl
      // this.nextEl = el
      if(el && el.previousElementSibling) {
        previousEl = el.previousElementSibling
        this.previousEl = previousEl
        this.nextEl = previousEl
        if(this.moveIndex >= 0) {
          // 元素在原本位置上方向下拖动时,除本元素外有位移的元素只是简单回到原位 
          el.style.transform = `translateY(0px)`
          this.moveY = this.moveY - el.clientHeight
          this.needMoveHeight = this.needMoveHeight - el.clientHeight
        } else {
          // 元素在原本位置下方向下拖动时, 除本元素外有位移的元素，只是向下移动拖动元素的高度
          this.moveY = this.moveY - previousEl.clientHeight
          previousEl.style.transform = `translateY(${this.srcElHeight}px)`
          this.needMoveHeight = this.needMoveHeight - previousEl.clientHeight
        }
        this.dragParentEl.style.transform = `translateY(${this.moveY}px)`
        
        this.canMoveUp = false
      } else {
        this.canMoveUp = false
        return
      }
    }
    // getElement(e, index) {
    //   console.log('点击的index', index)
    //   var odiv = e.srcElement
    //   let positionX, positionY
    //   var top, left
    //   var parentElement = odiv.parentElement
    //   // 父元素的上一个元素
    //   var previousElementSibling = odiv.parentElement.previousElementSibling
    //   // 父元素的下一个元素
    //   var nextParentElement = odiv.parentElement.nextElementSibling
    //   const {clientWidth, clientHeight} = odiv

    //   const placeEl = document.getElementsByClassName('drop-item-placeHolder')[index]
    //   placeEl.style.width = clientWidth + 'px'
    //   placeEl.style.height = clientWidth + 'px'
    //   placeEl.style.background = 'red'
    //   this.arr[index].showPlaceHolder = true
    //   this.$forceUpdate()
    //   //算出鼠标相对元素的位置
    //   let disX = e.clientX - odiv.offsetLeft;
    //   let disY = e.clientY - odiv.offsetTop;
    //   odiv.style.position = 'absolute'
    //   document.onmousemove = (e)=>{
    //     //用鼠标的位置减去鼠标相对元素的位置，得到元素的位置
    //     left = e.clientX - disX;  
    //     top = e.clientY - disY;
      
    //     //绑定元素位置到positionX和positionY上面
    //     positionX = top;
    //     positionY = left;
        
    //     //移动当前元素
        
    //     odiv.style.left = left + 'px';
    //     odiv.style.top = top + 'px';
        
    //   };
    //   document.onmouseup = (e) => {
    //     // 鼠标松开时确定元素位置
    //     if(clientHeight / 2  - top <= 0 && !!nextParentElement && Math.abs(left) <= clientWidth / 2) {
    //       let arr = this.arr
    //       const tem = JSON.parse(JSON.stringify(arr[index]))
    //       arr[index] = JSON.parse(JSON.stringify(arr[index + 1]))
    //       arr[index + 1] = JSON.parse(JSON.stringify(tem))
    //       this.arr = arr
    //       this.$forceUpdate()

    //     } else if(clientHeight / 2  + top <= 0 && previousElementSibling && Math.abs(left) <= clientWidth / 2) {
    //       let arr = this.arr
    //       const tem = arr[index]
    //       arr[index] = arr[index - 1]
    //       arr[index - 1] = tem
    //       this.arr = arr
    //       this.$forceUpdate()
    //     }
    //     this.arr[index].showPlaceHolder = false
    //     placeEl.style.width = 0
    //     placeEl.style.height = 0
    //     odiv.style.left = 0
    //     odiv.style.top = 0
    //     odiv.style.position = 'relative'
        
    //     document.onmousemove = null;
    //     document.onmouseup = null;
    //   }
    // },
    // reverserElement(el) {
    //   var previousElementSibling = el.parentElement.previousElementSibling
    //   // 父元素的下一个元素
    //   var nextParentElement = el.parentElement.nextElementSibling
    //   var gradeParentElement = el.parentElement.parentElement
    //   gradeParentElement.removeChild()
    // }
  },
}
</script>


