<template>
  <div class="nav-bar" :class="componentKey ? componentKey : ''"
       v-if="shownCategories.length > 0" @click="clickClose($event)">
    <div v-for="obj in shownCategories" class="nav-bar__link" :key="obj.name">
      <a v-if="obj.link" :href="obj.link" :class="obj.name === openCategory ? 'open' : ''">
        {{obj.name}}
        <button v-if="obj.hasChildren" class="nav-bar__button"
                :class="obj.name === openCategory ? 'open' : ''"
                @click.prevent="showMoreAction(obj)"></button>
      </a>
      <router-link v-else-if="obj.path" :to="obj.path"
                   :class="obj.name === openCategory ? 'open' : ''">
        {{obj.name}}
        <button v-if="obj.hasChildren" class="nav-bar__button" :class="obj.name === openCategory ? 'open' : ''"
                @click.prevent="showMoreAction(obj)"></button>
      </router-link>
      <div v-else-if="obj.html" v-html="obj.html"
           :class="obj.name === openCategory ? 'open' : ''"
           @click="openCategory = obj.name"></div>

      <div v-if="obj.hasChildren" class="nav-bar__link__open"
           :class="obj.name === openCategory ? 'open' : ''">
        <a v-if="obj.link" v-for="childObj in obj.children" :key="childObj.name"
           :href="childObj.link">{{childObj.name}}</a>
        <router-link v-else-if="obj.path" v-for="childObj in obj.children" :key="childObj.name"
                     :to="childObj.path">{{childObj.name}}
        </router-link>
      </div>
    </div>
    <div class="nav-bar__hidden">
      <div class="nav-bar__hidden-categories" v-if="hiddenCategories.length > 0"
           :class="showMore ? 'show' : ''">
        <div v-for="obj in hiddenCategories" :key="obj.name"
             class="nav-bar__link" :class="showMore ? 'show' : 'hide'">
          <a v-if="obj.link" :href="obj.link" :class="obj.name === openCategory ? 'open' : ''">
            {{obj.name}}
            <button v-if="obj.hasChildren" class="nav-bar__button"
                    :class="obj.name === openCategory ? 'open' : ''"
                    @click.prevent="openCategory === obj.name ? openCategory = '' : openCategory = obj.name"></button>
          </a>
          <router-link v-else-if="obj.path" :to="obj.path" :class="obj.name === openCategory ? 'open' : ''">
            {{obj.name}}
            <button v-if="obj.hasChildren" class="nav-bar__button"
                    :class="obj.name === openCategory ? 'open' : ''"
                    @click.prevent="openCategory === obj.name ? openCategory = '' : openCategory = obj.name"></button>
          </router-link>
          <div v-else-if="obj.html" v-html="obj.html"
               :class="obj.name === openCategory ? 'open' : ''"
               @click="openCategory = obj.name"></div>

          <div class="nav-bar__link__open" :class="obj.name === openCategory ? 'open' : ''">
            <a v-if="obj.link" v-for="childObj in obj.children" :key="childObj.name"
               :href="childObj.link">{{childObj.name}}</a>
            <router-link v-else-if="obj.path" v-for="childObj in obj.children" :key="childObj.name"
                         :to="childObj.path">{{childObj.name}}
            </router-link>
          </div>
        </div>
      </div>
      <button v-if="hiddenCategories.length > 0" class="nav-bar-toggler" type="button"
              @click="showMore = !showMore; openCategory = ''">
        <img :src="showMoreImage" alt="">
      </button>
    </div>
  </div>
</template>

<script>
  import imgShowMore from './assets/show-more.svg'

  export default {
    name: "VCollapsingMenu",
    props: ['navbar', 'externalPaddingMax', 'externalPaddingMin', 'componentKey'],
    data() {
      return {
        shownCategories: [],
        hiddenCategories: [],
        openCategory: '',
        allCategories: this.navbar || [],
        showMore: false,
        showMoreImage: imgShowMore,
      }
    },
    computed: {
      route() {
        return this.$route.path ? this.$route.path : window.location.href;
      }
    },
    created() {
      this.shownCategories = this.allCategories;
    },
    mounted() {
      const $this = this;
      window.addEventListener('resize', function () {
        $this.shownCategories = [];
        $this.hiddenCategories = [];
        if ($this.shownCategories.length === 0 && $this.hiddenCategories.length === 0) {
          $this.resize($this);
        }
      });
      for (let i in this.navbar) {
        let categoryChildNode = this.componentKey ? document.querySelector('.nav-bar.' + this.componentKey).childNodes[i] :
          document.querySelector('.nav-bar').childNodes[i];
        if (categoryChildNode && categoryChildNode.className === "nav-bar__link") {
          this.allCategories[i].width = categoryChildNode.clientWidth;
          if (parseInt(i) === (this.navbar.length - 1)) {
            this.shownCategories = [];
            this.resize(this);
          }
        }
      }
    },
    methods: {
      clickClose(e) {
        if (e.target.className === 'nav-bar__link' || e.target.className === 'nav-bar') {
          this.openCategory = ''
        }
      },
      resize($this) {
        //sum of external paddings and margins
        const parentElem = this.componentKey ? document.querySelector('.nav-bar.' + this.componentKey)
          : document.querySelector('.nav-bar'),
          clientWidth = parentElem ? parentElem.clientWidth - 60 : 0;
        let cutWidth = window.innerWidth > 1024 ? this.externalPaddingMin : this.externalPaddingMax;
        for (let i in this.allCategories) {
          if (cutWidth + $this.allCategories[i].width < clientWidth) {
            $this.shownCategories.push($this.allCategories[i]);
            cutWidth += $this.allCategories[i].width;
          } else {
            cutWidth += $this.allCategories[i].width;
            $this.hiddenCategories.push($this.allCategories[i])
          }
        }
      },
      showMoreAction(obj) {
        this.openCategory === obj.name ? this.openCategory = '' :
          this.openCategory = obj.name;
        this.showMore = false;
      }
    },
    watch: {
      route() {
        this.openCategory = '';
        this.showMore = false
      },
    }
  }
</script>

<style lang="scss">
  $defaultTextColor: #4F4F4F;
  $defaultBorderColor: #E0E0E0;
  $defaultActiveTextColor: #0079C2;

  .nav-bar {
    list-style: none;
    border-bottom: 1px solid rgba(0, 0, 0, .2);
    position: relative;
    display: flex;
    padding: 0 20px;
    min-width: 100%;
    @media (max-width: 1024px) {
      border-bottom: $defaultBorderColor 1px solid;
      justify-content: space-between;
      padding: 0;
      background: white;
      min-width: calc(100% - 50px);
    }
    &.open {
      z-index: 4;
      @media (max-width: 1024px) {
        height: 100%;
      }
    }
    &__link {
      position: relative;
      font-size: 18px;
      transition: all .5s ease-in-out;
      &.hide {
        font-size: 0;
        padding: 0;
        a {
          padding: 0;
          transition: all .5s ease-in-out;
        }
      }
      &.show {
        font-size: 18px;
      }
      a, a:active {
        display: flex;
        align-items: center;
        color: $defaultTextColor;
        white-space: nowrap;
        padding: 20px 20px 18px;
        border-bottom: transparent 2px solid;
        &.has-child {
          padding-right: 12px;
        }
        &:hover, &.open, &.router-link-active {
          color: $defaultActiveTextColor;
        }
        &.open {
          box-shadow: 0px -9px 12px rgba(0, 0, 0, 0.1)
        }
      }
      &__open {
        position: absolute;
        top: calc(100% + 1px);
        left: 0;
        background-color: white;
        box-shadow: 0 5px 12px rgba(0, 0, 0, 0.1);
        width: max-content;
        max-width: calc(100vw - 80px);
        min-width: calc(100% - 40px);
        border-bottom-left-radius: 6px;
        border-bottom-right-radius: 6px;
        transition: all .5s ease-in-out;
        opacity: 0;
        font-size: 0;
        z-index: 3;
        @media (max-width: 1024px) {
          top: 45px;
        }
        a {
          padding: 0;
          width: max-content;
          @media (max-width: 1024px) {
            width: auto;
            white-space: normal;
          }
        }
        &.open {
          padding: 20px;
          font-size: 16px;
          opacity: 1;
          a {
            display: block;
            padding: 10px 0;
          }
        }
      }
    }
    &__button {
      background: transparent url("./assets/down-black-arrow.svg") center center no-repeat;
      background-size: 14px;
      cursor: pointer;
      width: 24px;
      margin-left: 4px;
      height: 18px;
      transition: all .5s ease-in-out;
      &.open {
        transform: rotate(-180deg);
      }
    }

    &-toggler {
      padding: 17px 17px 0;
      background-color: transparent;
      @media screen and (-webkit-min-device-pixel-ratio:0)
      and (min-resolution:.001dpcm) {
        padding: 19px 17px 0;
      }
      img {
        width: 24px;
      }
    }

    &__hidden {
      position: relative;
      margin-right: 20px;
      &-categories {
        position: absolute;
        right: 2px;
        top: calc(100% + 1px);
        background: white;
        border: $defaultBorderColor 1px solid;
        box-shadow: 0 5px 12px rgba(0, 0, 0, 0.1);
        width: max-content;
        border-bottom-left-radius: 6px;
        border-bottom-right-radius: 6px;
        transition: all .5s ease-in-out;
        z-index: 2;
        opacity: 0;
        .nav-bar__link__open {
          left: unset !important;
          right: 2px;
        }
        &.show {
          opacity: 1;
        }
      }
    }
  }
</style>
