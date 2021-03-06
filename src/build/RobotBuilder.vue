<template>
  <div v-if="availableParts" class="content">
    <div class="preview">
      <CollapsibleSection>
        <div class="preview-content">
          <div class="top-row">
            <img :src="selectedRobot.head.src"/>
          </div>
          <div class="middle-row">
            <img :src="selectedRobot.leftArm.src" class="rotate-left"/>
            <img :src="selectedRobot.torso.src"/>
            <img :src="selectedRobot.rightArm.src" class="rotate-right"/>
          </div>
          <div class="bottom-row">
            <img :src="selectedRobot.base.src"/>
          </div>
        </div>
      </CollapsibleSection>
    <button class="add-to-cart" @click="addToCart()">Add to Cart</button>
    </div>
    <div class="top-row">
      <!-- aplica a class se for true -->
      <!-- <div :class="[saleBorderClass, 'top', 'part']"> -->
        <!-- div fica bloqueada e nunca é alterada, mesmo que haja funções sobre ela
        <div v-once class="robot-name">-->
        <!-- <div class="robot-name">
          {{selectedRobot.head.title}} -->
          <!-- v-if ignora/anula/remove o elemento da DOM dependendo da condição -->
          <!-- <span v-if="selectedRobot.head.onSale" class="sale">Sale!</span> -->
          <!-- v-show altera o css para display: none.
          É melhor usar quando o esforço do render é grande, pois altera apenas o css-->
          <!-- <span v-show="selectedRobot.head.onSale" class="sale">Sale!</span>
        </div>
      </div> -->
        <PartSelector
          :parts="availableParts.heads"
          position="top"
          @partSelected="part => selectedRobot.head = part"/>
    </div>
    <div class="middle-row">
        <PartSelector
          :parts="availableParts.arms"
          position="left"
          @partSelected="part => selectedRobot.leftArm = part"/>
        <PartSelector
          :parts="availableParts.torsos"
          position="center"
          @partSelected="part => selectedRobot.torso = part"/>
        <PartSelector
          :parts="availableParts.arms"
          position="right"
          @partSelected="part => selectedRobot.rightArm = part"/>
    </div>
    <div class="bottom-row">
      <PartSelector
        :parts="availableParts.bases"
        position="bottom"
        @partSelected="part => selectedRobot.base = part"/>
    </div>
  </div>
</template>

<script>
import PartSelector from './PartSelector.vue';
import CollapsibleSection from '../shared/CollapsibleSection.vue';

export default {
  name: 'RobotBuilder',
  created() {
    this.$store.dispatch('robots/getParts');
  },
  beforeRouteLeave(to, from, next) {
    if (this.addedToCart) {
      next(true);
    } else {
      /* eslint no-alert: 0 */
      /* eslint no-restricted-globals: 0 */
      const response = confirm('You have not added your robot to your cart, are you sure you want to leave?');
      next(response);
    }
  },
  components: { PartSelector, CollapsibleSection },
  data() {
    return {
      addedToCart: false,
      cart: [],
      selectedRobot: {
        head: {},
        leftArm: {},
        torso: {},
        rightArm: {},
        base: {},
      },
    };
  },
  computed: {
    availableParts() {
      return this.$store.state.robots.parts;
    },
    saleBorderClass() {
      return this.selectedRobot.head.onSale ? 'sale-border' : '';
    },
  },
  methods: {
    addToCart() {
      const robot = this.selectedRobot;
      const cost = robot.head.cost
        + robot.leftArm.cost
        + robot.torso.cost
        + robot.rightArm.cost
        + robot.base.cost;
        // dispatch actions; commit mutations;
      this.$store.dispatch('robots/addRobotToCart', { ...robot, cost })
        .then(() => this.$router.push('/cart'));
      this.addedToCart = true;
    },
  },
};
</script>

<!-- scoped para os estilos serem apenas aplicádos neste componente -->
<style scoped>
.part {
  position: relative;
  width:165px;
  height:165px;
  border: 3px solid #aaa;
}
.part img {
  width:165px;
}
.top-row {
  display:flex;
  justify-content: space-around;
}
.middle-row {
  display:flex;
  justify-content: center;
}
.bottom-row {
  display:flex;
  justify-content: space-around;
  border-top: none;
}
.head {
  border-bottom: none;
}
.left {
  border-right: none;
}
.right {
  border-left: none;
}
.left img {
  transform: rotate(-90deg);
}
.right img {
  transform: rotate(90deg);
}
.bottom {
  border-top: none;
}
.prev-selector {
  position: absolute;
  z-index:1;
  top: -3px;
  left: -28px;
  width: 25px;
  height: 171px;
}
.next-selector {
  position: absolute;
  z-index:1;
  top: -3px;
  right: -28px;
  width: 25px;
  height: 171px;
}
.center .prev-selector, .center .next-selector {
  opacity:0.8;
}
.left .prev-selector {
  top: -28px;
  left: -3px;
  width: 144px;
  height: 25px;
}
.left .next-selector {
  top: auto;
  bottom: -28px;
  left: -3px;
  width: 144px;
  height: 25px;
}
.right .prev-selector {
  top: -28px;
  left: 24px;
  width: 144px;
  height: 25px;
}
.right .next-selector {
  top: auto;
  bottom: -28px;
  left: 24px;
  width: 144px;
  height: 25px;
}
.right .next-selector {
  right: -3px;
}
.robot-name{
  position: absolute;
  top: -25px;
  text-align: center;
  width: 100%;
}
.sale {
  color: red;
}
.content {
  position: relative;
}
.add-to-cart {
  position: absolute;
  width: 210px;
  padding: 3px;
  font-size: 16px;
}
.sale-border {
  border: 3px solid rgb(151, 241, 7);
}
.preview {
  position: absolute;
  top: -20px;
  right: 0;
  width: 210px;
  height: 210px;
  padding: 5px;
}
.preview-content {
  border: 1px solid #999;
}
.preview img {
  width: 50px;
  height: 50px;
}
.rotate-right {
  transform: rotate(90deg);
}
.rotate-left {
  transform: rotate(-90deg);
}
</style>
