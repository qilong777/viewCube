<template>
  <div class="view-cube">
    <div class="cube" ref="cube" @mouseover="onMouseOverCube" @mouseout="onMouseOutCube" @click="onClickCube">
      <div class="front-wrap one">
        <div class="face front">前</div>
        <div class="side top-front"></div>
        <div class="side front-right"></div>
        <div class="side bottom-front"></div>
        <div class="side front-left"></div>

        <div class="vertice top-front-left"></div>
        <div class="vertice top-front-right"></div>
        <div class="vertice bottom-front-right"></div>
        <div class="vertice bottom-front-left"></div>

        </div>
      <div class="back-wrap one">
        <div class="face back">后</div>
        <div class="side top-back"></div>
        <div class="side back-left"></div>
        <div class="side bottom-back"></div>
        <div class="side back-right"></div>


        <div class="vertice top-back-right"></div>
        <div class="vertice top-back-left"></div>
        <div class="vertice bottom-back-left"></div>
        <div class="vertice bottom-back-right"></div>
        </div>
      <div class="top-wrap one">
        <div class="face top">上</div>
        <div class="side top-back"></div>
        <div class="side top-right"></div>
        <div class="side top-front"></div>
        <div class="side top-left"></div>

        <div class="vertice top-back-left"></div>
        <div class="vertice top-back-right"></div>
        <div class="vertice top-front-right"></div>
        <div class="vertice top-front-left"></div>


        </div>
      <div class="bottom-wrap one">
        <div class="face bottom">下</div>
        <div class="side bottom-front"></div>
        <div class="side bottom-right"></div>
        <div class="side bottom-back"></div>
        <div class="side bottom-left"></div>

        <div class="vertice bottom-front-left"></div>
        <div class="vertice bottom-front-right"></div>
        <div class="vertice bottom-back-right"></div>
        <div class="vertice bottom-back-left"></div>
        </div>
      <div class="left-wrap one">
        <div class="face left">左</div>
        <div class="side top-left"></div>
        <div class="side front-left"></div>
        <div class="side bottom-left"></div>
        <div class="side back-left"></div>

        <div class="vertice top-back-left"></div>
        <div class="vertice top-front-left"></div>
        <div class="vertice bottom-front-left"></div>
        <div class="vertice bottom-back-left"></div>
        </div>
      <div class="right-wrap one">
        <div class="face right">右</div>
        <div class="side top-right"></div>
        <div class="side back-right"></div>
        <div class="side bottom-right"></div>
        <div class="side front-right"></div>


        <div class="vertice top-front-right"></div>
        <div class="vertice top-back-right"></div>
        <div class="vertice bottom-back-right"></div>
        <div class="vertice bottom-front-right"></div>

        </div>

      <div class="x"></div>
      <div class="y"></div>
    </div>
  </div>
</template>

<script>
import { Str2ViewType, ViewType, ViewType2Rotate } from '../../common/const';
import EventEmitter from '../../dxfThree/common/EventEmitter';
import GlobalIndex from '../../views/build/globalIndex';

const partArr = ['side', 'vertice', 'face'];
export default {
  name: 'ViewCube',
  data() {
    return {
      activeEle: null,
    };
  },
  methods: {
    // 移入事件
    onMouseOverCube(e) {
      let classList = e.target.classList;
      let elements = this.getAllDomsByClassList(classList);
      if (elements) {
        for (let i = 0; i < elements.length; i += 1) {
          const ele = elements[i];
          ele.classList.add('hover');
        }
      }
    },
    // 移出事件
    onMouseOutCube(e) {
      let classList = e.target.classList;
      let elements = this.getAllDomsByClassList(classList);
      if (elements) {
        for (let i = 0; i < elements.length; i += 1) {
          const ele = elements[i];
          ele.classList.remove('hover');
        }
      }
    },
    // 点击事件
    onClickCube(e) {
      let target = e.target;
      let classList = target.classList;
      if (this.activeEle) {
        let oldDoms = this.getAllDomsByClassList(this.activeEle.classList);
        if (oldDoms) {
          for (let i = 0; i < oldDoms.length; i += 1) {
            const dom = oldDoms[i];
            dom.classList.remove('active');
          }
        }
      }

      let newDoms = this.getAllDomsByClassList(classList);
      if (!newDoms) {
        return;
      }

      this.activeEle = target;
      for (let i = 0; i < newDoms.length; i += 1) {
        const dom = newDoms[i];
        dom.classList.add('active');
      }

      let detailPart = classList[1];
      let viewType = Str2ViewType[detailPart];
      GlobalIndex.getInstance().threeDCanvas.switchAngleOfView(viewType);
      // this.rotateCube(viewType);
    },
    // 根据视角旋转立方体
    rotateCube(viewType) {
      let cube = this.$refs.cube;
      cube.style.transition = 'transform 0.5s';
      let [x, y] = ViewType2Rotate[viewType];
      let originTransform = cube.style.transform;
      if (originTransform) {
        let [ox, oy] = this.getRotateXY(originTransform);
        let y1 = y;
        let x1 = x;
        if (y > 0) {
          y1 = (y - 360);
        } else {
          y1 = (y + 360);
        }
        let dis1 = Math.abs(y - oy);
        let dis2 = Math.abs(y1 - oy);
        y = dis1 < dis2 ? y : y1;

        if (x > 0) {
          x1 = (x - 360);
        } else {
          x1 = (x + 360);
        }
        dis1 = Math.abs(x - ox);
        dis2 = Math.abs(x1 - ox);
        x = dis1 < dis2 ? x : x1;
      }

      cube.style.transform = `rotateX(${x}deg) rotateY(${y}deg)`;
    },

    // 根据tranform获取旋转的XY
    getRotateXY(originTransform) {
      if (originTransform) {
        let x = 0;
        let y = 0;
        let data = originTransform.match(/rotateX\(([-\d]*)deg\)/);
        if (data) {
          x = data[1];
        }
        data = originTransform.match(/rotateY\(([-\d]*)deg\)/);
        if (data) {
          y = data[1];
        }
        return [x, y];
      }
      return [0, 0];
    },

    // 根据classList获取对应的doms
    getAllDomsByClassList(classList) {
      if (!classList) {
        return null;
      }
      let part = classList[0];
      if (part && partArr.includes(part)) {
        let detailPart = classList[1];
        let elements = document.getElementsByClassName(detailPart);
        return elements;
      }
      return null;
    },
    // 初始化
    init() {
      if (this.activeEle) {
        let doms = this.getAllDomsByClassList(this.activeEle.classList);
        if (doms) {
          for (let i = 0; i < doms.length; i += 1) {
            const ele = doms[i];
            ele.classList.remove('active');
          }
        }
      }
      this.activeEle = document.querySelector('.top-front-left');
      let doms = this.getAllDomsByClassList(this.activeEle.classList);
      if (doms) {
        for (let i = 0; i < doms.length; i += 1) {
          const ele = doms[i];
          ele.classList.add('active');
        }
      }
    },
  },
  created() {
    EventEmitter.on('initViewCube', () => {
      this.init();
    });

    EventEmitter.on('rotateCubeWithAngle', (data) => {
      let [x, y, z] = data;
      let cube = this.$refs.cube;
      cube.style.transition = 'transform 0s';
      cube.style.transform = `rotateX(${x}deg) rotateY(${y}deg) rotateZ(${z}deg)`;
    });
  },
  destroyed() {
    EventEmitter.off('initViewCube');
    EventEmitter.off('rotateCubeWithAngle');
  },


};
</script>

<style lang="scss">
$sideLen:80px;
$subLen:10px;
.view-cube{
  position: absolute;
  width: 200px;
  height: 200px;
  right: 240px;
  top: 40px;
  // background-color: #000;
  perspective: 800px;
  .cube{
    position: relative;
    top: $sideLen - 2 * $subLen;
    left: $sideLen - 2 * $subLen;
    width: $sideLen;
    height: $sideLen;
    transform-style: preserve-3d;
    transition: transform 0.5s;
    // transform: rotateX(45deg) rotateY(45deg) rotateZ(45deg);
    .one{
      position: absolute;
      width: $sideLen;
      height: $sideLen;
      border: 1px solid #000;
      .face{
        position: absolute;
        top: $subLen;
        left: $subLen;
        width: $sideLen - 2 * $subLen;
        height: $sideLen - 2 * $subLen;
        background-color: #ccc;
        line-height: $sideLen - 2 * $subLen;
        text-align: center;
        font-size: 24px;
        &.hover{
          background-color: #00ffff;
        }
        &.active{
          background-color: #f40;
        }

      }
      .side{
          position: absolute;
          width: $subLen;
          height: $sideLen - 2 * $subLen;
          background-color: #ccc;
          &:nth-of-type(2){
            width: $sideLen - 2 * $subLen;
            height: $subLen;
            top: 0px;
            left: $subLen;
          }
          &:nth-of-type(3){
              top: $subLen;
              right: 0px;
          }
          &:nth-of-type(4){
            width: $sideLen - 2 * $subLen;
            height: $subLen;
            bottom: 0;
            left: $subLen;
          }
          &:nth-of-type(5){
              top: $subLen;
              left: 0px;
          }
          &.hover{
            background-color: #00ffff;
          }
          &.active{
          background-color: #f40;
        }
        }
      .vertice{
        position: absolute;
        width: $subLen;
        height: $subLen;
        background-color: #ccc;
        &:nth-of-type(6){
          top: 0px;
          left: 0px;
        }
        &:nth-of-type(7){
          top: 0px;
          right: 0px;
        }
        &:nth-of-type(8){
          bottom: 0px;
          right: 0px;
        }
        &:nth-of-type(9){
          bottom: 0px;
          left: 0px;
        }
        &.hover{
          background-color: #00ffff;
        }
        &.active{
          background-color: #f40;
        }
      }

    }


    .front-wrap {
        transform: translateZ($sideLen / 2);
    }
    .x,.y {
      position: absolute;
      top: $sideLen/2;
      left: $sideLen/2;
      width: 200px;
      height: 2px;
      background-color: #ff0000;
    }
    .y {
      height: 200px;
      width:  2px;
      background-color: #00ff00;
    }
    .back-wrap {
        transform: rotateY(180deg) translateZ($sideLen / 2);
    }
    .top-wrap {
        transform: rotateX(90deg) translateZ($sideLen / 2);
    }
    .bottom-wrap {
        transform: rotateX(-90deg) translateZ($sideLen / 2);
    }
    .left-wrap {
        transform: rotateY(-90deg) translateZ($sideLen / 2);
    }
    .right-wrap {
        transform: rotateY(90deg) translateZ($sideLen / 2);
    }
  }
}

</style>
