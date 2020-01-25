<template>
  <div class="container">
    <div class="row">
      <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
        <h1>Animations</h1>
        <hr />
        <select v-model="alertAnimation">
          <option value="fade">Fade</option>
          <option value="slide">Slice</option>
        </select>
        <br />
        <br />
        <button class="btn btn-primary" @click="show = !show">
          Show Alert
        </button>
        <br />
        <br />
        <transition :name="alertAnimation" :type="dynamicType" mode="out-in">
          <div class="alert alert-info" v-if="show" key="1">
            This is some info.
          </div>
          <div class="alert alert-warning" v-else key="2">
            This is some warning.
          </div>
        </transition>
        <transition :name="alertAnimation" :type="dynamicType">
          <div class="alert alert-info" v-show="show">This is some info.</div>
        </transition>
        <transition name="fade">
          <div class="alert alert-info" v-show="show">This is some info.</div>
        </transition>
        <transition name="slide" type="animation">
          <div class="alert alert-info" v-if="show">This is some info.</div>
        </transition>
        <transition name="fade" appear>
          <div class="alert alert-info" v-if="show">This is some info.</div>
        </transition>
        <transition
          enter-active-class="animated bounce"
          leave-active-class="animated shake"
          appear
        >
          <div class="alert alert-info" v-if="show">This is some info.</div>
        </transition>
        <hr />
        <button class="btn btn-primary" @click="load = !load">
          Show / Remove
        </button>
        <br /><br />
        <transition
          @before-enter="beforeEnter"
          @enter="enter"
          @after-enter="afterEnter"
          @enter-cancelled="enterCancelled"
          @before-leave="beforeLeave"
          @leave="leave"
          @after-leave="afterLeave"
          @leave-cancelled="leaveCancelled"
          :css="false"
        >
          <div
            style="width:300px; height:100px; background-color:lightgreen;"
            v-if="load"
          ></div>
        </transition>
        <hr />
        <button
          class="btn btn-primary"
          @click="
            selectedComponent =
              selectedComponent == 'app-success-alert'
                ? 'app-danger-alert'
                : 'app-success-alert'
          "
        >
          Toogle Components
        </button>
        <br /><br />
        <transition name="fade" appear mode="out-in">
          <component :is="selectedComponent"></component>
        </transition>
        <hr />
        <button class="btn btn-primary" @click="addItem">Add Item</button>
        <br /><br />
        <ul class="list-group">
          <transition-group name="slide">
            <li
              class="list-group-item pointer"
              v-for="(n, i) in numbers"
              :key="i"
              @click="numbers.splice(i, 1)"
            >
              {{ n }}
            </li>
          </transition-group>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import dangerAlert from './DangerAlert.vue';
  import successAlert from './SuccessAlert.vue';

  export default {
    components: {
      appDangerAlert: dangerAlert,
      appSuccessAlert: successAlert
    },
    data() {
      return {
        show: false,
        load: true,
        alertAnimation: 'fade',
        elementWidth: 100,
        selectedComponent: 'app-success-alert',
        numbers: [1, 2, 3, 4, 5]
      };
    },
    computed: {
      dynamicType() {
        return this.alertAnimation == 'fade' ? 'transition' : 'animation';
      }
    },
    methods: {
      beforeEnter(el) {
        console.log('beforeEnter');
        this.elementWidth = 100;
        el.style.width = this.elementWidth + 'px';
      },
      enter(el, done) {
        console.log('enter');
        let round = 1;
        const interval = setInterval(() => {
          el.style.width = this.elementWidth + round * 10 + 'px';
          round++;
          if (round > 20) {
            clearInterval(interval);
            done();
          }
        }, 20);
      },
      afterEnter(el) {
        console.log('afterEnter');
      },
      enterCancelled(el) {
        console.log('enterCancelled');
      },
      beforeLeave(el) {
        console.log('beforeLeave');
        this.elementWidth = 300;
        el.style.width = this.elementWidth + 'px';
      },
      leave(el, done) {
        console.log('leave');
        let round = 1;
        const interval = setInterval(() => {
          el.style.width = this.elementWidth - round * 10 + 'px';
          round++;
          if (round > 20) {
            clearInterval(interval);
            done();
          }
        }, 20);
      },
      afterLeave(el) {
        console.log('afterLeave');
      },
      leaveCancelled(el) {
        console.log('leaveCancelled');
      },
      addItem() {
        const pos = Math.floor(Math.random() * this.numbers.length);
        this.numbers.splice(pos, 0, this.numbers.length + 1);
      }
    }
  };
</script>

<style>
  .fade-enter {
    opacity: 0;
  }
  .fade-enter-active {
    transition: opacity 1s;
  }
  .fade-leave {
    /* opacity: 1; */
  }
  .fade-leave-active {
    transition: opacity 1s;
    opacity: 0;
  }

  .slide-enter {
    opacity: 0;
  }
  .slide-enter-active {
    animation: slide-in 1s ease-out forwards;
    transition: opacity 0.5s;
  }
  .slide-leave {
  }
  .slide-leave-active {
    animation: slide-out 1s ease-out forwards;
    transition: opacity 1s;
    opacity: 0;
    position: absolute;
  }

  .slide-move {
    transition: transform 1s;
  }

  @keyframes slide-in {
    from {
      transform: translateY(20px);
    }
    to {
      transform: translateY(0);
    }
  }
  @keyframes slide-out {
    from {
      transform: translateY(0);
    }
    to {
      transform: translateY(20px);
    }
  }

  .pointer {
    cursor: pointer;
  }
  .pointer:hover {
    background-color: #ddd;
  }
</style>
