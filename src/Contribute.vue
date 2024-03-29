<template>
  <div id="contribute">
    <div class="notice" v-if="notice">
      {{ notice }}
    </div>

    <div class="title">Help us make some avocado toast!</div>

    <div class="desc">
      <div class="desc-text">
        <p>
          <span class="ext-name">{{ extName }}</span> is a project fueled by
          love and crunchy toast, created for everyone to freely use and
          improve.
        </p>
        <p>
          You can support our goals and make a difference by sharing some
          avocados with us! Every ounce will help add new features and keep
          things afloat.
        </p>
      </div>
      <img class="desc-image" :src="`./assets/avocado-toast.jpg`" />
    </div>

    <transition name="goals">
      <div class="goals-wrap" v-if="goals">
        <div class="cta">Support our current goals</div>
        <div class="goals">
          <div class="goal" v-for="goal in goals.items" :key="goal.id">
            <div class="goal-bullet">•</div>
            {{ goal }}
          </div>
        </div>
        <v-linear-progress
          class="progress"
          :progress="goals.progress.value / goals.progress.goal"
          open
        >
        </v-linear-progress>
        <div class="progress-details">
          <div>
            Raised
            <span class="progress-value">{{ goals.progress.value }}</span>
            <img class="progress-token" :src="`./assets/avocado.svg`" />
            of
            <span class="progress-value">{{ goals.progress.goal }}</span>
            <img class="progress-token" :src="`./assets/avocado.svg`" />
            goal
          </div>
          <div>
            <span class="progress-value">1</span>
            <img class="progress-token" :src="`./assets/avocado.svg`" />
            =
            {{ goals.progress.currency.symbol
            }}<span class="progress-value"
              >{{ goals.progress.currency.exchangeRate }}
            </span>
          </div>
        </div>
      </div>
    </transition>

    <div class="cta-buttons">
      <v-button class="contribute-button" @click="contribute('patreon')" raised>
        <img class="patreon-image" :src="`./assets/patreon.png`" />
      </v-button>
      <v-button class="contribute-button" @click="contribute('paypal')" raised>
        <img class="paypal-image" :src="`./assets/paypal.png`" />
      </v-button>
    </div>

    <div class="cta-coin" @click="contribute('bitcoin')">
      <img :src="`./assets/bitcoin.svg`" />
      <div>BITCOIN</div>
    </div>
  </div>
</template>

<script>
import {Button, LinearProgress} from 'ext-components';

export default {
  name: 'v-contribute',

  components: {
    [Button.name]: Button,
    [LinearProgress.name]: LinearProgress
  },

  props: {
    extName: {
      type: String,
      required: true
    },
    extSlug: {
      type: String,
      required: true
    },
    notice: {
      type: String,
      default: ''
    }
  },

  emits: ['open'],

  data: function () {
    return {
      goals: null
    };
  },

  methods: {
    contribute: function (service) {
      const url = `https://armin.dev/go/${service}?pr=${this.extSlug}&src=app`;

      this.$emit('open', {url});
    }
  },

  mounted: async function () {
    const rsp = await fetch(
      `https://api.armin.dev/v1/contribute/goals/${this.extSlug}`
    );
    const goals = await rsp.json();

    const exchangeRate = goals.progress.currency.exchangeRate;
    goals.progress.value = Math.trunc(goals.progress.value / exchangeRate);
    goals.progress.goal = Math.trunc(goals.progress.goal / exchangeRate);

    this.goals = goals;
  }
};
</script>

<style lang="scss">
$mdc-theme-primary: #1abc9c;

@import '@material/button/mixins';
@import '@material/theme/mixins';
@import '@material/typography/mixins';

#contribute {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 856px;
}

.notice {
  @include mdc-typography(body2);
  @include mdc-theme-prop(color, text-primary-on-light);
  margin-top: 12px;
  max-width: 90vw;
  padding: 12px 24px;
  background-color: #ecf0f1;
  border-radius: 3px;
}

.title {
  @include mdc-typography(headline6);
  @include mdc-theme-prop(color, text-primary-on-light);
  margin-top: 24px;
  max-width: 90vw;
}

.desc {
  display: grid;
  align-items: center;
  justify-items: center;
  grid-row-gap: 24px;
  margin-top: 24px;
}

.desc-text {
  @include mdc-typography(body1);
  @include mdc-theme-prop(color, text-primary-on-light);
  font-size: 0.938rem;
  max-width: 90vw;
}

.desc-text .ext-name {
  font-weight: 500;
}

.desc-image {
  max-width: 60vw;
}

.goals-wrap {
  margin-top: 36px;
  width: 90vw;
}

.cta {
  @include mdc-typography(headline6);
  @include mdc-theme-prop(color, text-primary-on-light);
  text-align: center;
}

.goals {
  @include mdc-typography(body1);
  @include mdc-theme-prop(color, text-primary-on-light);
  font-size: 0.938rem;
}

.goals {
  margin-top: 24px;
  margin-bottom: 12px;
}

.goal {
  display: flex;
  align-items: center;
}

.goal-bullet {
  font-size: 36px;
  color: $mdc-theme-primary;
  margin-right: 8px;
}

.progress {
  border-radius: 3px;
  height: 5px;

  & .mdc-linear-progress__bar-inner {
    border-top: 5px solid $mdc-theme-primary;
  }
}

.progress-details {
  display: flex;
  justify-content: space-between;
  @include mdc-typography(caption);
  @include mdc-theme-prop(color, text-secondary-on-light);
  font-weight: 500;
  margin-top: 12px;
}

.progress-value {
  font-weight: 700;
}

.progress-token {
  width: auto;
  height: 14px;
  vertical-align: -10%;
  opacity: 0.7;
}

.goals-enter-active,
.goals-leave-active {
  max-height: 300px;
  margin-top: 24px;
  transition: max-height 0.4s ease, margin-top 0.4s ease, opacity 0.3s ease;
}

.goals-enter-from,
.goals-leave-to {
  max-height: 0;
  margin-top: 0;
  opacity: 0;
}

.cta-buttons {
  display: grid;
  grid-row-gap: 24px;
  margin-top: 48px;
}

.cta-coin {
  display: grid;
  align-items: center;
  justify-items: center;
  grid-template-columns: repeat(2, auto);
  grid-column-gap: 8px;
  margin-top: 32px;
  margin-bottom: 24px;
  @include mdc-typography(subtitle1);
  font-weight: 700;
  color: #3498db;
  cursor: pointer;
}

.cta-coin:hover,
.cta-coin:active {
  color: #2980b9;
}

.contribute-button {
  display: flex;
  align-items: center;
  justify-content: center;
  @include mdc-button-container-fill-color(#ffad01);
  @include mdc-button-shape-radius(24px);
  width: 192px !important;
  height: 48px !important;
}

.contribute-button img {
  width: auto;
  height: 20px;
}

.cta-coin img {
  width: 20px;
  height: 20px;
}

@media (min-width: 480px) {
  .cta-buttons {
    grid-template-columns: repeat(2, 1fr);
    grid-column-gap: 56px;
    margin-top: 56px;
  }

  .desc-image {
    max-width: 260px;
  }
}

@media (min-width: 576px) {
  .title {
    @include mdc-typography(headline5);
  }

  .desc-text {
    max-width: 70vw;
  }

  .goals-wrap {
    width: 70vw;
  }
}

@media (min-width: 768px) {
  .title {
    @include mdc-typography(headline4);
    @include mdc-theme-prop(color, text-primary-on-light);
    margin-top: 48px;
  }

  .desc {
    grid-template-columns: repeat(2, auto);
    grid-column-gap: 56px;
    margin-top: 72px;
  }

  .desc-text {
    @include mdc-typography(subtitle1);
    max-width: 400px;
  }

  .desc-image {
    max-width: 300px;
  }

  .goals-wrap {
    margin-top: 48px;
    width: 100%;
  }

  .cta {
    @include mdc-typography(headline5);
  }

  .goals {
    @include mdc-typography(subtitle1);
  }

  .goals {
    margin-top: 48px;
  }

  .progress-details {
    @include mdc-typography(body2);
  }

  .progress-token {
    height: 16px;
  }

  .goals-enter-active,
  .goals-leave-active {
    margin-top: 48px;
  }
}
</style>
