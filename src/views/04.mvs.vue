<template>
  <div class="mvs-container">
    <div class="filter-wrap">
      <div class="seciton-wrap">
        <!-- 分类切换 地区 -->
        <span class="section-type">地区:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item, index) in areas" :key="index">
            <span
              class="title"
              :class="{ active: area == item }"
              @click="area = item"
            >
              {{item}}
            </span>
          </li>
        </ul>
      </div>
      <div class="type-wrap">
        <span class="type-type">类型:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item, index) in types" :key="index">
            <span
              class="title"
              :class="{ active: type == item }"
              @click="type = item"
            >
              {{item}}
            </span>
          </li>
        </ul>
      </div>
      <div class="order-wrap">
        <span class="order-type">排序:</span>
        <ul class="tabs-wrap">
          <li class="tab" v-for="(item, index) in orders" :key="index">
            <span
              class="title"
              :class="{ active: order == item }"
              @click="order = item"
            >
              {{item}}
            </span>
          </li>
        </ul>
      </div>
    </div>
    <!-- mv容器 -->
    <!-- 推荐MV -->
    <div class="mvs">
      <div class="items">
        <div class="item" v-for="(item, index) in list" :key="index">
          <div class="img-wrap">
            <img :src="item.cover" alt="" />
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num">{{ item.playCount }}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{ item.name }}</div>
            <div class="singer">{{ item.artistName }}</div>
          </div>
        </div>
      </div>
      <!-- 分页器 -->
      <el-pagination
        @current-change="handleCurrentChange"
        background
        layout="prev, pager, next"
        :total="total"
        :current-page="page"
        :page-size="5"
        :limit="limit"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
  // 导入 axios
  import axios from 'axios'
  export default {
    name: 'mvs',
    data() {
      return {
        // 总条数
        total: 20,
        // 页码
        page: 1,
        // 页容量
        limit: 8,
        // 地区
        areas: ['全部','内地','港台','欧美','日本','韩国'],
        area: '全部',
        // 类型
        types: ['全部','官方版','原声','现场版','网易出品'],
        type: '全部',
        // 排序
        orders: ['上升最快','最热','最新'],
        order: '上升最快',
        // 查询的结果
        list: [],
        
      }
    },
    // 侦听器
    watch: {
      area() {
        // 返回第一页
        this.page = 1
        // 获取数据
        this.getList()
      },
      type() {
        // 返回第一页
        this.page = 1
        // 获取数据
        this.getList()
      },
      order() {
        // 返回第一页
        this.page = 1
        // 获取数据
        this.getList()
      }
    },
    created() {
      // 获取数据
      this.getList()
    },
    methods: {
      // 获取列表数据
      getList() {
        // 获取数据
        axios({
          url: 'https://autumnfish.cn/mv/all',
          method: 'get',
          params: {
            area: this.area,
            type: this.type,
            order: this.order,
            // 数量
            limit: this.limit,
            // 偏移值 分页 (页码-1)*数量
            offset: (this.page - 1) * this.limit
          }
        }).then(res => {
          // console.log(res)
          this.list = res.data.data
          // 处理次数
          for (let i = 0; i < this.list.length; i++) {
            if (this.list[i].playCount > 100000) {
              this.list[i].playCount =
                parseInt(this.list[i].playCount / 10000) + '万'
            }
          }

          // 保存总条数
          // 接口问题 有count 就赋值
          if (res.data.count) {
            this.total = res.data.count
          }
        })
      },
      // 页码改变的回调函数
      handleCurrentChange(val) {
        // console.log(`当前页: ${val}`)
        // 保存页面 重新获取数据
        this.page = val
        this.getList()
      }
    }
  }
</script>

<style></style>
