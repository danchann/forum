<template>
	<div
		class='thread_display'
		@click.self='goToThread'
	>
		<div class='thread_display__border' :style='{"background-color" : thread.Category.color}'></div>
		<avatar-icon ref='avatar' :user='thread.User' size='small' class='thread_display__icon'></avatar-icon>
		<div style='width: 100%;'>
			<div class='thread_display__header' @click.self='goToThread'>
				<span class='thread_display__name' @click='goToThread'>
					{{thread.name}}
				</span>
				<div class='thread_display__meta_bar' @click.self='goToThread'>
					<div @click.self='goToThread'>
						By
						<span class='thread_display__username' ref='username' @click='goToUser'>{{threadUsername}}</span>
						in
						<span class='thread_display__category' ref='category' @click='goToCategory'>{{thread.Category.name}}</span>
						&middot;
						<span class='thread_display__date' @click='goToThread'>{{thread.createdAt | formatDate}}</span>
					</div>
				</div>
			</div>
			<div class='thread_display__replies_bar' @click.self='goToThread'>
				<div
					class='thread_display__latest_reply'
					v-if='thread.Posts.length === 2'
					@click='goToPost'
				>
					<span class='fa fa-reply fa-fw'></span>
					<span class='thread_display__latest_reply__text'>Latest reply by</span>
					<span class='thread_display__username'>{{replyUsername}}</span>
					&middot;
					<span class='thread_display__date'>{{thread.Posts[1].createdAt | formatDate}}</span>
				</div>
				<span style='cursor: default;' v-else>No replies</span>
				<div class='thread_display__replies' title='Replies to thread' @click='goToThread'>
					<span class='fa fa-comment-o fa-fw'></span>
					{{thread.postsCount - 1}}
				</div>
			</div>
			<div class='thread_display__content' @click='goToThread'>
				{{thread.Posts[0].content | stripTags | truncate(150)}}
			</div>
		</div>
	</div>
</template>

<script>	
	import AvatarIcon from './AvatarIcon'

	export default {
		name: 'ThreadDisplay',
		props: ['thread'],
		components: {
			AvatarIcon
		},
		computed: {
			threadUsername () {
				if(this.thread.User) {
					return this.thread.User.username
				} else {
					return '[deleted]'
				}
			},
			replyUsername () {
				if(this.thread.Posts[1].User) {
					return this.thread.Posts[1].User.username
				} else {
					return '[deleted]'
				}
			}
		},
		methods: {
			goToUser () {
				this.$router.push('/user/' + this.thread.User.username)
			},
			goToThread () {
				this.$router.push('/thread/' + this.thread.slug + '/' + this.thread.id)
			},
			goToCategory () {
				this.$router.push('/category/' + this.thread.Category.value.toLowerCase())
			},
			goToPost () {
				this.$router.push(
					'/thread/' +
					this.thread.slug + '/' +
					this.thread.id + '/' +
					this.thread.Posts[1].postNumber
				)
			}
		}
	}
</script>

<style lang='scss' scoped>
	@import '../assets/scss/variables.scss';

	.thread_display {
		display: flex;
		padding: 0.75rem;
		margin-bottom: 1rem;
		background-color: #fff;
		border-radius: 0.25rem;
		transition: background-color 0.2s, box-shadow 0.2s;
		position: relative;
		border: thin solid $color__gray--darker;

		&:hover {
			@extend .shadow_border--hover;
		}

		@at-root #{&}__icon {
			margin-right: 0.5rem;
		}

		@at-root #{&}__username,
		         #{&}__category {
			
			transition: opacity 0.2s;
			cursor: pointer;
			color: $color--text__primary;

			&:hover {
				opacity: 0.75;
			}
		}	
		@at-root #{&}__date {
			color: $color--text__primary;
		}

		@at-root #{&}__header {
			display: flex;
			justify-content: space-between;
		}
			@at-root #{&}__name {
				font-weight: 500;
				font-size: 1.25rem;
				cursor: default;
			}
			@at-root #{&}__meta_bar {
				display: flex;
				cursor: default;
				color: $color--gray__darkest;
				justify-content: space-between;
			}

		@at-root #{&}__replies_bar {
			display: flex;
			justify-content: space-between;
		}
			@at-root #{&}__latest_reply {
				cursor: pointer;
				transition: opacity 0.2s;
				color: $color--text__secondary;

				&:hover {
					opacity: 0.75;
				}

				.fa {
					color: $color--text__primary;
					font-size: 0.75rem;
				}
			}
			@at-root #{&}__replies {
				cursor: default;
				width: 4rem;
				text-align: right;
			}
	
		@at-root #{&}__content {
			margin-top: 0.5rem;
			word-break: break-all;
		}
	}

	@media (max-width: 420px) {
		.thread_display {
			@at-root #{&}__header {
				flex-direction: column;
			}
			@at-root #{&}__meta_bar {
				font-size: 0.9rem;
				margin-bottom: 0.25rem;
			}

			@at-root #{&}__content {
				display: none;
			}

			@at-root #{&}__replies_bar {
				position: relative;
				left: -3.25rem;
				width: calc(100% + 3.25rem);
			}

			@at-root #{&}__latest_reply {
				.fa {
					margin-right: 0.25rem;
				}

				@at-root #{&}__text {
					display: none;
				}
			}
		}
	}
</style>