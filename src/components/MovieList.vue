<template>
  <div>
    <el-container>
      <el-header>
        <el-col :span="6">
          <el-input v-model="search_key" placeholder="请输入需要检索的电影名称"></el-input>
        </el-col>
        <el-col :span="1">
          <el-button @click="search()">搜索</el-button>
        </el-col>
        <el-col :span="10" class="header-text">电 影 列 表</el-col>
        <el-col :span="6">
          <div class="block">
            <el-pagination
              @current-change="handleCurrentChange"
              layout="prev, pager, next"
              :current-page="currentPage"
              :page-size="pageSize"
              :total="totalMovie"
            ></el-pagination>
          </div>
        </el-col>
      </el-header>
      <el-container>
        <el-aside>
          <div class="boarding">
            <Boarding />
          </div>
        </el-aside>
        <el-main>
          <el-row v-for="(movie,index) in movieShowed" :key="index">
            <el-col :span="24">
              <MovieCard :detail="movie" />
            </el-col>
          </el-row>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import MovieCard from "./MovieCard";
import Boarding from "./Boarding";
import axios from "axios";

export default {
  name: "MovieList",
  components: {
    MovieCard,
    Boarding
  },
  data() {
    return {
      movies: [],
      currentPage: 1,
      pageSize: 10,
      totalMovie: 0,
      search_key: "",
      oldSearch: "",
      genre: ""
    };
  },

  mounted: function() {
    this.search();
  },
  computed: {
    movieShowed: function() {
      return this.movies.slice(
        (this.currentPage - 1) * this.pageSize,
        this.currentPage * this.pageSize
      );
    }
  },
  methods: {
    handleCurrentChange(val) {
      this.currentPage = val;
    },
    searchTitle(movieTitle) {
      this.search = movieTitle;
    },
    search:function() {
      axios
        .post(`/api/query_movie_list`, { start_from: 0, limitation: 200,search_key:this.search_key })
        .then(res => {
          this.movies = res.data;
          this.totalMovie = res.data.length;
        })
        .catch(res => {});
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
  font-size: 20px;
}

.header-text {
  font-size: 28px;
  font-family: "Times New Roman", Times, serif;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
  width: 25%;
}

.el-main {
  background-color: #e9eef3;
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

.block {
  float: right;
  padding-top: 15px;
}

.el-dropdown-link {
  cursor: pointer;
  color: white;
}

.el-icon-arrow-down {
  font-size: 12px;
}

.boarding {
  padding-left: 8px;
}
</style>
