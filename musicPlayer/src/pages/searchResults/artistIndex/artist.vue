<!--
 * @Author: 李浩栋
 * @Begin: 2019-08-31 11:26:07
 * @Update: 2019-11-14 15:29:23
 * @Update log: 更新日志
 -->
<template>
  <div class="wrapper pd23">
    <div v-show="!load">
      <artist
        v-for="(item, index) in allArtistList"
        :key="index"
        :circle="true"
        :ImgUrl="item.img1v1Url"
        :alia="item.alias.length > 0 ? item.alias[0] : ''"
        line="one"
        :name="item.name"
        :isIn="item.accountId"
      ></artist>
      <info :info="info" :keywords="keywords"></info>
    </div>
    <page-loading v-show="load"></page-loading>
  </div>
</template>

<script>
import api from 'api'
import info from 'base/pageErrorInfo'
import artist from 'base/interchangeable'
import pageLoading from 'base/pageLoading'
import { filterSetKeyWords } from 'utils/setKeyWords'

export default {
  name: '',
  data () {
    return {
      allArtistList: [],
      info: false,
      load: true
    }
  },
  props: {
    // 搜索关键字
    keywords: {
      type: String
    }
  },
  created () {
    this.load = true
    this._getAllArtistList(this.keywords)
  },
  methods: {
    _getAllArtistList (key) {
      api.searchFn(key, undefined, undefined, 100)
        .then((res) => {
          const data = res.data
          if (data.code === 200) {
            if (this.allArtistList.length) {
              this.allArtistList = [this.allArtistList, ...data.result.artists]
            } else {
              this.allArtistList = data.result.artists
            }
            this.allArtistList = filterSetKeyWords(this.keywords, this.allArtistList, 'name')
            this.load = false
            if (data.result.artistCount === 0) {
              this.info = true
            }
          }
        })
        .catch(error => {
          this.load = false
          this.info = true
          console.log(error)
        })
    }
  },
  components: {
    artist,
    info,
    pageLoading
  }
}
</script>

<style lang='less' scoped>
@import url("~styles/global.less");
</style>
