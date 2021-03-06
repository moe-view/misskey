<template>
<mk-ui>
	<div class="xygkxeaeontfaokvqmiblezmhvhostak" v-if="!fetching">
		<div class="is-suspended" v-if="user.isSuspended"><fa icon="exclamation-triangle"/> {{ $t('@.user-suspended') }}</div>
		<div class="is-remote" v-if="user.host != null"><fa icon="exclamation-triangle"/> {{ $t('@.is-remote-user') }}<a :href="user.url || user.uri" target="_blank">{{ $t('@.view-on-remote') }}</a></div>
		<main>
			<div class="main">
				<x-header :user="user"/>
				<mk-note-detail v-for="n in user.pinnedNotes" :key="n.id" :note="n" :compact="true"/>
				<x-timeline class="timeline" ref="tl" :user="user"/>
			</div>
			<div class="side">
				<div class="instance" v-if="!$store.getters.isSignedIn"><mk-instance/></div>
				<x-profile :user="user"/>
				<x-integrations :user="user"/>
				<mk-calendar @chosen="warp" :start="new Date(user.createdAt)"/>
				<mk-activity :user="user"/>
				<x-photos :user="user"/>
				<x-friends :user="user"/>
				<x-followers-you-know v-if="$store.getters.isSignedIn && $store.state.i.id != user.id" :user="user"/>
				<div class="nav"><mk-nav/></div>
			</div>
		</main>
	</div>
</mk-ui>
</template>

<script lang="ts">
import Vue from 'vue';
import i18n from '../../../../i18n';
import parseAcct from '../../../../../../misc/acct/parse';
import Progress from '../../../../common/scripts/loading';
import XHeader from './user.header.vue';
import XTimeline from './user.timeline.vue';
import XProfile from './user.profile.vue';
import XPhotos from './user.photos.vue';
import XFollowersYouKnow from './user.followers-you-know.vue';
import XFriends from './user.friends.vue';
import XIntegrations from './user.integrations.vue';

export default Vue.extend({
	i18n: i18n(),
	components: {
		XHeader,
		XTimeline,
		XProfile,
		XPhotos,
		XFollowersYouKnow,
		XFriends,
		XIntegrations
	},
	data() {
		return {
			fetching: true,
			user: null
		};
	},
	watch: {
		$route: 'fetch'
	},
	created() {
		this.fetch();
	},
	methods: {
		fetch() {
			this.fetching = true;
			Progress.start();
			this.$root.api('users/show', parseAcct(this.$route.params.user)).then(user => {
				this.user = user;
				this.fetching = false;
				Progress.done();
			});
		},

		warp(date) {
			(this.$refs.tl as any).warp(date);
		}
	}
});
</script>

<style lang="stylus" scoped>
.xygkxeaeontfaokvqmiblezmhvhostak
	max-width 980px
	min-width 720px
	padding 16px
	margin 0 auto

	> .is-suspended
	> .is-remote
		margin-bottom 16px
		padding 14px 16px
		font-size 14px
		box-shadow var(--shadow)
		border-radius var(--round)

		&.is-suspended
			color var(--suspendedInfoFg)
			background var(--suspendedInfoBg)

		&.is-remote
			color var(--remoteInfoFg)
			background var(--remoteInfoBg)

		> a
			font-weight bold

	> main
		display flex
		justify-content center

		> .main
		> .side
			> *:not(:last-child)
				margin-bottom 16px

		> .main
			flex 1
			min-width 0 // SEE: http://kudakurage.hatenadiary.com/entry/2016/04/01/232722
			margin-right 16px

			> .timeline
				box-shadow var(--shadow)

		> .side
			width 275px
			flex-shrink 0

			> p
				display block
				margin 0
				padding 0 12px
				text-align center
				font-size 0.8em
				color var(--text)

			> .instance
				box-shadow var(--shadow)
				border-radius var(--round)

			> .nav
				padding 16px
				font-size 12px
				color var(--text)
				background var(--face)
				box-shadow var(--shadow)
				border-radius var(--round)

				a
					color var(--text)99

				i
					color var(--text)

</style>
