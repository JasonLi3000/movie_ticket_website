<template>
  <div>
    <el-container>
      <el-header>{{this.detail.title}}</el-header>
      <el-main>
        <div class="container">
          <img :src="poster" :alt="title" @error="imgError()" class="poster" />
          <div style="flex: 1;">
            <div class="casts">
              <div>
                导演:
                <span v-for="(person, index) in directors" :key="index+100" class="director">
                  {{person.name}}
                  <a v-if="person!=directors[directors.length-1]" class="director">, </a>
                </span>
              </div>
              <div>
                编剧:
                <span v-for="(person, index) in writers" :key="index+1000">
                  {{person.name}}<a v-if="person!=writers[writers.length-1]" class="sep">, </a>
                </span>
              </div>
              <div>
                演员:
                <span v-for="(person, index) in casts" :key="index+1000">
                  {{person.name}}
                  <a v-if="person!=casts[casts.length-1]" class="sep">, </a>
                </span>
              </div>
            </div>
            <div class="check">
              上映日期:
              <span v-for="(date, index) in pubdate" :key="index" class="date">{{date}}</span>
              <div class="duration">
                片长: {{duration}}min
              </div>
              类型:
              <span v-for="(genre, index) in genres" :key="index+10" class="genre">{{genre}}</span>
              <div>
                制片国家/地区:
                <span v-for="(country, index) in countries" :key="index+110" class="genre">{{country}}</span>
              </div>
              <div>
                语言:
                <span v-for="(language, index) in languages" :key="index+120" class="genre">{{language}}</span>
              </div>
              <div style="color: #337ab7; font-weight: bold">
                票价: {{this.price}} 元
              </div>
            </div>
          </div>
          <div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="rate"
                  disabled
                  text-color="#ff9900"
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="score" :style="{'color':getScoreColor}">{{rating.average}}</span>
              <span class="rating-people">({{rating.rating_people}}人评分)</span>
            </div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="value5"
                  disabled
                  show-text
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  text-color="#ff9900"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="rating-people">{{rating.stars[0]}}%</span>
            </div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="value4"
                  disabled
                  show-text
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  text-color="#ff9900"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="rating-people">{{rating.stars[1]}}%</span>
            </div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="value3"
                  disabled
                  show-text
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  text-color="#F7BA2A"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="rating-people">{{rating.stars[2]}}%</span>
            </div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="value2"
                  disabled
                  show-text
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  text-color="#99A9BF"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="rating-people">{{rating.stars[3]}}%</span>
            </div>
            <div>
              <span class="rating">
                <el-rate
                  v-model="value1"
                  disabled
                  show-text
                  :colors="['#99A9BF', '#F7BA2A', '#FF9900']"
                  text-color="#99A9BF"
                  score-template="{value}">
                </el-rate>
              </span>
              <span class="rating-people">{{rating.stars[4]}}%</span>
            </div>
          </div>
        </div>
        <div class="intro">
          <div class="intro-word">简介</div><br>
          <div>{{summary}}</div>
        </div>
        <div class="buy">
          <el-button type="primary" @click="openDialog">购 票</el-button>
          <el-dialog
            :visible.sync="dialogVisible"
            width="30%"
            :before-close="handleClose">
            <div style="height: 100px">
              <span>购 票 数 目</span>
              <el-input-number v-model="ticketNum" @change="handleChange" :min="1" :max="10" label="购票数目"></el-input-number>
            </div>
            <span slot="footer" class="dialog-footer">
                  <el-button @click="dialogVisible = false">取 消</el-button>
                  <el-button type="primary" @click="this.buyTicket">确 定</el-button>
                </span>
          </el-dialog>
        </div>
        <div class="review">
          <div class="review-word">评价</div><br>
          <div v-for="item in reviews">
            <div>
              <div style="color: RGB(57, 38, 184)">匿名用户{{item.review_id}}</div>
              <div style="color: RGB(31, 158, 255)">评分：{{item.ranking}}</div>
            </div>
            <div>{{item.content}}</div>
            <br>
            <br>
          </div>
        </div>
        <el-button type="primary" @click="goToList">返回电影列表</el-button>
      </el-main>
    </el-container>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: "Detail",
    data() {
      return {
        movies: [],
        detail: [],

        title: "",
        poster: "",
        directors: [],
        writers: [],
        casts: [],
        pubdate: [],
        duration: "",
        genres: [],
        countries: [],
        languages: [],

        rating: {
          average: 3,
          rating_people: 0,
          stars: []
        },
        rate: 3,
        value1: 1,
        value2: 2,
        value3: 3,
        value4: 4,
        value5: 5,

        summary: "",

        price: 0,
        dialogVisible: false,
        ticketNum: 1,

        reviews: [{
          "content": "好看",
          "ranking": 10,
          "review_id": 1
        },
          {
            "content": "不好看",
            "ranking": 0,
            "review_id": 2
          }]
      }
    },
    mounted: function() {
      axios.post(`/api/query_movie/${this.$route.params.id}`)
        .then((res) => {
          this.title = res.data.information.title
          this.poster = res.data.information.poster
          this.directors = res.data.information.directors
          this.writers = res.data.information.writers
          this.casts = res.data.information.casts
          this.pubdata = res.data.information.pubdata
          this.duration = res.data.information.duration
          this.genres = res.data.information.genres
          this.countries = res.data.information.countries
          this.languages = res.data.information.languages
          this.rating = res.data.information.rating
          this.rate = parseFloat((parseFloat(res.data.information.rating.average)/2).toFixed(1))
          if(res.data.information.rating.average === "") {
            this.rate = 0
            this.rating.average = ""
            this.rating.rating_people = 0
            this.rating.stars = [0, 0, 0, 0, 0]
          }
          this.summary = res.data.information.summary
          this.price = res.data.information.price
          this.ticketNum = 1
          this.reviews = res.data.reviews
        })
        .catch(this.getJsonInfo())
      console.log("电影ID：" + this.$route.params.id)
    },
    methods: {
      getJsonInfo: function() {
        this.$http.options.emulateJSON = true
        this.$http.get('./static/films.json').then(function(response){
          this.movies = response.data
          this.detail = this.movies
            .filter((info) => {
              const text = Object.values(info._id).join('').toLowerCase()
              return text.search(this.$route.params.id) >= 0
            })[0]

          this.totalMovie = this.movies.length
          this.initFromNative()
        }).catch(function(response){
        })
      },
      initFromNative: function() {
        this.casts = this.detail.casts
        this.countries = this.detail.countries
        this.directors = this.detail.directors
        this.duration = this.detail.duration
        this.genres = this.detail.genres
        this.languages = this.detail.languages
        this.poster = this.detail.poster
        this.pubdate = this.detail.pubdate
        this.rating = this.detail.rating
        this.summary = this.detail.summary
        this.title = this.detail.title
        this.writers = this.detail.writers
        this.countries = this.detail.countries
        this.languages = this.detail.languages
        this.rate = parseFloat((parseFloat(this.detail.rating.average)/2).toFixed(1))
        if(this.detail.rating.average === "") {
          this.rate = 0
          this.rating.average = ""
          this.rating.rating_people = 0
          this.rating.stars = [0, 0, 0, 0, 0]
        }
        console.log(this.rate)
        console.log(this.rating)
      },
      imgError() {
        this.poster = require('../assets/LostSource.png')
      },
      goToDetail(movie) {
        this.$router.push({name: "movie-detail", params: {id: movie._id}})
      },
      goToList() {
        this.$router.push({name: "movie-list"})
      },
      openDialog() {
        this.ticketNum = 1
        this.dialogVisible = true
      },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done()
          })
          .catch(_ => {})
      },
      handleChange(value) {
        console.log(value)
      },
      buyTicket() {
        axios.post(`/api/insert_order`,
          {
            'item': {
            'movie_id': this.$route.params.id,
            'movie_name': this.movie_name,
            'price_sum': this.price * this.ticketNum,
            'movie_num': this.ticketNum
          }
        })
        this.dialogVisible = false
        this.$message({
          message: '购票成功',
          type: 'success'
        })
      }
    },
    computed: {
      getScoreColor() {
        if(this.rating.average > 7.9)
          return '#FF9900'
        else if(this.rating.average > 4)
          return '#F7BA2A'
        else
          return '#FF9900'
      }
    },
    watch: {
      '$route' (to, from) {
        this.$router.go(0)
      }
    },
  }
</script>

<style scoped>
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #337ab7;
    font-size: 30px;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }

  body > .el-container {
    margin-bottom: 40px;
  }

  .el-container:nth-child(5) .el-aside,
  .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }

  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }

  .container {
    display: flex;
    padding: 10px;
    padding-left: 20%;
    padding-right: 20%;
    text-align: left;
    line-height: 1.6;
  }
  .header {
    display: flex;
    align-items: left;
    font-size: 20px;
  }
  .poster {
    margin-right: 12px;
    flex-shrink: 0;
    width: 160px;
    height: 240px;
  }
  .check {
    font-size: 15px;
    opacity: 1;
    font-weight: 350;
    letter-spacing: 0.5px;
  }
  .genre {
    float: inherit
  }
  .genre::after {
    content: "  ";
    color: #ddd;
  }
  .duration::after {
    content: "  ";
    color: #ddd;
  }
  .score {
    color: #f7ba2a;
    font-size: 15px;
    opacity: 1;
  }
  .rating {
    display: inline-table
  }
  .rating-people {
    padding-left: 5px;
    font-size: 12px;
  }
  .casts {
    font-size: 16px;
    opacity: 1;
    font-weight: 400;
    letter-spacing: 0.5px;
  }
  .sep {
    color: black;
  }
  .director {
    color: black
  }
  .intro {
    opacity: 1;
    line-height: 1.4;
    letter-spacing: 0.5px;
    font-size: 14px;
    padding-top: 5px;
    word-break: break-all;
    padding-left: 20%;
    padding-right: 20%;
    text-align: left;
  }
  .intro-word {
    color: #337ab7;
    font-size: 20px;
  }
  .review {
    opacity: 1;
    line-height: 1.4;
    letter-spacing: 0.5px;
    font-size: 14px;
    padding-top: 5px;
    word-break: break-all;
    padding-left: 20%;
    padding-right: 20%;
    text-align: left;
  }
  .review-word {
    color: #337ab7;
    font-size: 20px;
  }
</style>
