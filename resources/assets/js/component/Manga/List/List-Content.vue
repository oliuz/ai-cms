<template>
	<li class="ribbon ribbon-modern ribbon-primary ribbon-right">
		<div class="ribbon-box"><i class="fa fa-fw fa-heartbeat"></i></div>
		<a :href="item.manga_url" class="link-effect">
			<img :src="item.thumb_url" class="img-avatar" alt="">
			{{ item.title }}
			<br>
			<b class="arf-small">
				<i class="fa fa-star text-warning"></i>
				<i class="fa fa-star text-warning"></i>
				<i class="fa fa-star text-warning"></i>
				<i class="fa fa-star text-warning"></i>
				<i class="fa fa-star-half-o text-warning"></i>
				{{ item.category.category }}
			</b>
		</a>
		<ul class="nav-users left-padding-fix">
			<li v-for="chapter in sortChapter">
				<a :href="chapter.chapter_url" class="link-effect">
					<h6 class="text-ellipsis">
						{{ chapter.chapter_num }}
						<i class="fa fa-angle-double-right"></i>
						{{ chapter.chapter_title }}
						<span class="pull-right">{{ chapter.release_date }}</span>
					</h6>
				</a>
			</li>
		</ul>
	</li>
</template>

<script>
	export default {
		data() {
			return {};
		},
		props: {
			item: { type: Object, required: true }
		},
		computed: {
			sortChapter() {
				var chapters = this.item.latest_chapters;

				return chapters.sort(function (a, b) {
					if (a.chapter_num > b.chapter_num) return -1;
					if (a.chapter_num < b.chapter_num) return 1;
					return 0;
				});
			}
		},
		methods: {
		}
	}
</script>
