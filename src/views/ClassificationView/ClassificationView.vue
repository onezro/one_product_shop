<template>
  <div class="cation">
    <div class="header">
      <div class="title">
        <span>商品分类</span>
      </div>
      <div class="search">
        <van-search
          v-model="value"
          shape="round"
          placeholder="请输入搜索关键词"
        />
        <div class="search-cover" @click.stop="showSearch = true"></div>
      </div>
    </div>
    <van-popup
      v-model="showSearch"
      position="right"
      :style="{ width: '100%', height: '100%' }"
    >
      <SearchView @cancel="cancel" />
    </van-popup>
    <div class="classList">
      <div class="loading" v-if="loading">
        <van-loading text-color="red" color="red" size="24px" vertical
          >加载中...</van-loading
        >
      </div>

      <div class="classList-left" ref="sidebar">
        <van-sidebar v-model="activeKey" @change="onChange">
          <van-sidebar-item
            v-for="(c, i) in classGoodlist"
            :title="c.cate_name"
            :key="i"
          />
        </van-sidebar>
      </div>
      <div
        class="list"
        ref="scrollbar"
        id="list1"
        v-for="b in showList"
        :key="b.cate_name"
      >
        <wd-tabs v-model="active" sticky :map-num="4">
          <wd-tab
            v-for="c in b.children"
            :title="c.cate_name"
            :key="c.cate_name"
          >
            <div class="good">
              <div class="good-name">{{ c.cate_name }}</div>
              <div class="good-card">
                <div
                  class="card"
                  v-for="a in c.children"
                  :key="a.cate_name"
                  @click="goToClassify(a.store_category_id)"
                >
                  <img v-lazy="a.pic" :alt="a.cate_name" />
                  <p>{{ a.cate_name }}</p>
                </div>
              </div>
            </div>
          </wd-tab>
        </wd-tabs>
      </div>
    </div>
  </div>
</template>
<script>
import { getClassGoods } from "@/api/classification";
import SearchView from "@/views/Search/SearchView.vue";
export default {
  data() {
    return {
      classGoodlist: [],
      loading: true,
      value: "",
      activeKey: 0,
      listscrollTop: 0,
      active: 0,
      chooseList: 0,
      showSearch: false,
    };
  },
  mounted() {
    this.getClassList();
  },
  watch: {
    listscrollTop(a) {
      this.activeTitle(a);
    },
    activeKey(a) {
      this.active = 0;
      if (a >= 5) {
        this.$refs.sidebar.scrollTop = (a - 5) * 48;
      }
    },
  },
  computed: {
    showList() {
      return this.classGoodlist.filter((t, i) => {
        if (i == this.chooseList) {
          return t;
        }
      });
    },
  },
  methods: {
    async getClassList() {
      let { data } = await this.$axios(getClassGoods);
      this.classGoodlist = data.list;
      // console.log(this.classGoodlist);
      this.loading = false;
    },
    onChange(index) {
      this.chooseList = index;
    },
    goToClassify(id) {
      this.$router.push(`/classify?classify_id=${id}`);
    },
    cancel() {
      this.showSearch = false;
    },
  },
  components: {
    SearchView,
  },
};
</script>
<style lang="scss" scoped>
.cation {
  background-color: #f7f8fa;
  .header {
    display: flex;
    flex-direction: column;
    position: sticky;
    top: 0;
    left: 0;
    z-index: 10;
    .title {
      display: flex;
      width: 100%;
      padding: 10px 0;
      background-color: #f7f8fa;
      span {
        margin: auto;
      }
    }
    .search{
      position: relative;
      .search-cover{
        position: absolute;
        width: 100%;
        height: 100%;
        left: 0;
        top: 0;
      }
    }
    .van-search {
      padding: 5px 12px;
      height: 34px;
      .van-search__content {
        height: 24px;
        .van-cell {
          padding: 0;
          .van-field__body {
            .van-field__control {
              height: 24px;
            }
          }
        }
      }
    }
  }
  .classList {
    position: sticky;
    top: 70px;
    left: 0;
    display: flex;
    gap: 5px;
    justify-content: space-between;
    .loading {
      position: absolute;
      padding-top: 75%;
      padding-left: 45%;
    }
    .classList-left {
      width: 21%;
      height: calc(100vh - 120px);
      overflow: scroll;
      &::-webkit-scrollbar {
        display: none;
      }
      .van-sidebar-item {
        padding: 14px 12px;
        background-color: white;
        z-index: 100;
      }
      .van-sidebar-item--select::before {
        left: 0;
        top: 0;
        width: 2px;
        height: 48px;
        transform: scale(1);
        transform-origin: top;
        transition: all 0.5s ease-in-out;
      }
      .van-sidebar-item--select,
      .van-sidebar-item--select:active {
        background-color: #f7f8fa;
        color: red;
        font-weight: bold;
        // transition: all .5s linear;
      }
      .van-sidebar-item--select:active::before {
        transform: scale(0);
      }
    }

    .list {
      // position: relative;
      flex: 1;
      height: calc(100vh - 120px);
      overflow: scroll;
      &::-webkit-scrollbar {
        display: none;
      }
      .good {
        display: flex;
        flex-direction: column;
        .good-name {
          font-size: 14px;
          font-weight: bold;
          padding: 17px 12px;
        }
        .good-card {
          border-top-left-radius: 10px;
          border-bottom-left-radius: 10px;
          // box-shadow: 1px 5px 5px gray;
          padding: 10px 20px;
          display: flex;
          background-color: #fff;
          flex-wrap: wrap;
          gap: 20px 34px;
          .card {
            // margin: 10px 0;

            display: flex;
            flex-direction: column;
            align-items: center;
            width: 60px;
            img {
              margin: auto;
              width: 60px;
              height: 60px;
            }
            p {
              width: 60px;
              font-size: 12px;
              color: #888;
              text-align: center;
              line-height: 15px;
              overflow: hidden;
              text-overflow: ellipsis;
              white-space: nowrap;
            }
          }
        }
      }
      .wd-tabs__line {
        background: linear-gradient(315deg, #f05151 0, #f57676 100%);
      }
    }
  }
  @keyframes load {
    0% {
      transform: translateY(48px);
    }
    100% {
      transform: translateY(0);
    }
  }
}
</style>