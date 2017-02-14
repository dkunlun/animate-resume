<template>
  <div class="resumeEditor" :class="{htmlMode:enableHtml}" ref="container">
    <div v-if="enableHtml" v-html="result"></div>
    <pre v-else>{{result}}</pre>
  </div>
</template>

<script>
	import marked from 'marked'

	export default {
		props: ['markdown', 'enableHtml'],
		computed: {
			result: function () {
				return this.enableHtml ? marked(this.markdown) : this.markdown
			}
		},
		methods: {
			goBottom() {
				this.$refs.container.scrollTop = 10000
			}
		}
	}
</script>

<style scoped>
  .htmlMode {
    animation: flip 2s;
  }
  
  @keyframes flip {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
</style>