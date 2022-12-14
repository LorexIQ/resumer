<template>
  <div class="l-sidebar">
    <span
      class="l-sidebar__title "
      :class="{'l-sidebar__title--animate': changingTitle}"
    >
      {{pageTitle}}
    </span>
    <div
      class="l-sidebar__theme"
      @click="themeSelect(false)"
    >
      <template
        v-for="(mode, index) in themeModes"
      >
        <transition name="theme-switch-icon">
          <lfa :icon="['fas', mode]" v-if="themeModeSelected === index"/>
        </transition>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "Sidebar",
  data() {
    return {
      pageTitle: '',
      changingTitle: false,
      titleDelayId: null,

      themeModes: [
        'a',
        'moon',
        'sun'
      ],
      themeModeSelected: 0,
    }
  },
  props: {
    pagesData: {
      type: Array,
      default: () => {
        return [];
      }
    }
  },
  mounted() {
    this.pageTitle = this.titlePage;
    if (localStorage.themeModeSelected) {
      this.themeModeSelected = parseInt(localStorage.themeModeSelected);
    }
    this.themeSelect(true);
  },
  computed: {
    titlePage() {
      if (this.pagesData.filter(res => res.name === this.$nuxt.$route.name)[0]) {
        return this.pagesData.filter(res => res.name === this.$nuxt.$route.name)[0].title;
      } else {
        return 'Error'
      }
    }
  },
  watch: {
    titlePage() {
      this.changePageAnimation(this.titlePage);
    }
  },
  methods: {
    themeSelect(update=false) {
      if (!update) {
        this.themeModeSelected++;
        if (this.themeModeSelected > 2) this.themeModeSelected = 0;
        localStorage.themeModeSelected = this.themeModeSelected;
      }
      if (this.themeModeSelected === 0) {
        this.$colorMode.auto('light');
      } else if (this.themeModeSelected === 1) {
        this.$colorMode.clear();
      } else if (this.themeModeSelected === 2) {
        this.$colorMode.setMode('light');
      }
    },
    changePageAnimation(newTitle) {
      if (this.changingTitle) {
        this.changingTitle = false;
        clearTimeout(this.titleDelayId);
      }
      setTimeout(() => {
        this.changingTitle = true;
        this.titleDelayId = setTimeout(() => {
          this.pageTitle = newTitle;
          this.titleDelayId = setTimeout(() => {
            this.changingTitle = false;
          }, 700);
        }, 300);
      });
    },
  }
}
</script>

<style lang="scss" scoped>
.l-sidebar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 100px;
  user-select: none;
  &__title {
    overflow: hidden;
    font-size: 35px;
    font-weight: 500;
    letter-spacing: -1px;
    &--animate {
      animation: page-title 1s;
      animation-iteration-count: 1;
    }
  }
  &__theme {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 50px;
    height: 50px;
    background-color: var(--light-purple);
    color: var(--c-sd-text);
    border-radius: 15px;
    cursor: pointer;
    transition: .3s;
    svg {
      position: absolute;
      font-size: 20px;
    }
    &:hover {
      background-color: var(--light-dark-purple);
    }
  }
}
@keyframes page-title {
  0% {
    max-width: 150px;
  }
  30% {
    max-width: 0;
  }
  50% {
    max-width: 0;
  }
  100% {
    max-width: 150px;
  }
}
.theme-switch-icon {
  &-enter-active, &-leave-active {
    transition: .3s;
  }
  &-enter, &-leave-to {
    transform: scale(0.5);
    opacity: 0;
  }
  &-leave, &-enter-to {
    opacity: 1;
  }
}
</style>
