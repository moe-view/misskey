<template>
<div class="azibmfpleajagva420swmu4c3r7ni7iw">
	<h1>{{ $t('share-with', { name }) }}</h1>
	<div>
		<mk-signin v-if="!$store.getters.isSignedIn"/>
		<mk-post-form v-else-if="!posted" :initial-text="text" :instant="true" @posted="posted = true"/>
		<p v-if="posted" class="posted"><fa icon="check"/></p>
	</div>
	<ui-button class="close" v-if="posted" @click="close">{{ $t('@.close') }}</ui-button>
</div>
</template>

<script lang="ts">
import Vue from 'vue';
import i18n from '../../../i18n';

export default Vue.extend({
	i18n: i18n('mobile/views/pages/share.vue'),
	data() {
		return {
			name: null,
			posted: false,
			text: new URLSearchParams(location.search).get('text')
		};
	},
	methods: {
		close() {
			window.close();
		}
	},
	mounted() {
		this.$root.getMeta().then(meta => {
			this.name = meta.name;
		});
	}
});
</script>

<style lang="stylus" scoped>
.azibmfpleajagva420swmu4c3r7ni7iw
	> h1
		margin 8px 0
		color #555
		font-size 20px
		text-align center

	> div
		max-width 500px
		margin 0 auto

		> .posted
			display block
			margin 0 auto
			padding 64px
			text-align center
			background #fff
			border-radius 6px
			width calc(100% - 32px)

	> .close
		display block
		margin 16px auto
		width calc(100% - 32px)
</style>
