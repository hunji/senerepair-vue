<template>
  <el-container>
    <el-aside width="200px">
      <h3>视频列表:</h3>
      <div v-for="item in 8" v-bind:key="item" style="margin:10px;">
        <span>在线播放视频{{item}}</span>
      </div>
    </el-aside>
    <el-container>
      <el-header>
        <h3>在线播放</h3>
        <hr>
      </el-header>
      <el-main>
        嵌入播放器
      </el-main>
    </el-container>
  </el-container>
</template>
