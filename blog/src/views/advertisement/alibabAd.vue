<template>
  <v-carousel continuous cycle hide-delimiter-background show-arrows-on-hover>
    <v-carousel-item
        v-for="(item, i) in items"
        :key="i"
        :href="items.url"
        :src="item.src"
        reverse-transition="fade-transition"
        transition="fade-transition"
    ></v-carousel-item>
  </v-carousel>
</template>

<script>
export default {
  data: () => ({
    isCycle: true,
    items: [
      {
        src:
            "https://static.yxin.top/upload/photos/9fd3de132e7b72f7accadb2ee34c1fdb.jpg",
        url:
            "https://www.aliyun.com/daily-act/ecs/activity_share?userCode=4w54sa3f"
      },
      {
        src:
            "https://static.yxin.top/upload/photos/988f442c3f7d5870f6b562b414002836.png",
        url: "https://www.aliyun.com/minisite/goods?userCode=4w54sa3f"
      },
      {
        src:
            "https://static.yxin.top/upload/photos/8699fb077e8645f4117c91a01fc2cf42.jpg",
        url: "https://www.aliyun.com/activity/daily/bestoffer?userCode=4w54sa3f"
      },
      {
        src:
            "https://static.yxin.top/upload/photos/4d5b7a02d871d7176292c75968b2edaf.png",
        url: "https://yqh.aliyun.com/live/cdncarnival?userCode=4w54sa3f"
      }
    ]
  }),
  methods: {}
};
</script>

<style scoped></style>
