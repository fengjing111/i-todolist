<template>
  <div class="head">
    <div class="title">待办事项</div>
    <el-input
      v-model="value"
      placeholder="输入文本并按回车添加"
      style="width: 500px"
      @change="addInput"
    ></el-input>
    <div class="food">
      <div class="add">未完成的待办事项列表</div>
      <div
        class="todo"
        v-for="item in Notfinished"
        :key="item.id"
        @mouseenter="enter(item)"
        @mouseleave="leave(item)"
      >
        <div class="checkbox">
          <input type="checkbox" v-model="item.switch" @click="changebox(item)" />
        </div>
        <span
          ref="spaninput"
          class="inputNotfinished"
          :class="item.switch ? 'line-through' : ''"
          :contenteditable="item.isShow"
          @keyup.enter="close(item)"
          @click="changebox(item)"
          >{{ item.value }}</span
        >
        <p>{{ item.time }}</p>
        <el-dropdown trigger="click">
          <span class="el-dropdown-link">
            <i class="el-icon-s-operation btn" v-show="item.flag"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item @click.native="amend(item)">修改</el-dropdown-item>
            <el-dropdown-item @click.native="del(item.id)">删除</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="add">已完成的待办事项列表</div>
      <div
        class="todo"
        v-for="item in finished"
        :key="item.id"
        @mouseenter="enter(item)"
        @mouseleave="leave(item)"
      >
        <div class="checkbox">
          <input type="checkbox" v-model="item.switch" @click="changebox(item)" />
        </div>
        <span
          class="inputNotfinished"
          :class="item.switch ? 'line-through' : ''"
          @click="changebox(item)"
          >{{ item.value }}</span
        >
        <p>{{ item.time }}</p>
        <el-dropdown trigger="click">
          <span class="el-dropdown-link">
            <i class="el-icon-s-operation btn" v-show="item.flag"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item @click.native="amend(item.id)" :class="item.switch ? 'disnone' : ''"
              >修改</el-dropdown-item
            >
            <el-dropdown-item @click.native="del(item.id)">删除</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </div>
  </div>
</template>
<script>
import dayjs from 'dayjs'
export default {
  data () {
    return {
      // input 双向绑定的value
      value: '',
      // 未完成的数组
      Notfinished: [
        {
          id: 1,
          value: 12313,
          // 选择框的开关
          switch: false,
          // 时间
          time: dayjs(new Date()).format('YYYY-MM-DD'),
          // 鼠标悬浮的隐藏和显示
          flag: false,
          // 控制span修改value
          isShow: false
        },
        {
          id: 2,
          value: 12314,
          switch: false,
          time: dayjs(new Date()).format('2018-02-19'),
          flag: false,
          isShow: false
        },
        {
          id: 3,
          value: 12315,
          switch: false,
          time: dayjs(new Date()).format('2019-02-19'),
          flag: false,
          isShow: false
        }
      ],
      // 已完成的数组
      finished: []
    }
  },
  created () {
  },
  methods: {
    // 添加
    addInput () {
      if (this.value === '') {
        alert('请输入便签名')
      }

      this.switch = false
      this.Notfinished.unshift({
        id: Math.random() * 100,
        value: this.value,
        switch: false,
        time: dayjs(new Date()).format('YYYY-MM-DD')
      })
      this.value = ''
      // console.log(1)
    },
    // 删除
    del (id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.Notfinished = this.Notfinished.filter(item => {
          return item.id !== id
        })
        this.finished = this.finished.filter(item => {
          return item.id !== id
        })
        console.log(id)
        console.log(this.Notfinished)
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
      })
      console.log(id)
    },
    // 点击加入已完成
    changebox (item) {
      if (item.switch !== true) {
        this.Notfinished.map(obj => {
          if (obj.id === item.id) {
            obj.switch = true
            this.finished.push(obj)
            this.Notfinished.splice(item, 1)
          }
        })
      } else {
        this.finished.forEach(obj => {
          if (obj.id === item.id) {
            item.switch = false
            this.Notfinished.push(obj)
            this.finished.splice(obj, 1)
          }
        })
      }
      // if (item.switch !== true) {
      //   for (let i = 0; i < this.Notfinished.length; i++) {
      //     if (this.Notfinished[i].id === item.id) {
      //       this.Notfinished[i].switch = true
      //       this.finished.push(this.Notfinished[i])
      //       this.Notfinished.splice(i, 1)
      //     }
      //   }
      //   this.finished.sort(function (a, b) {
      //     return a.time < b.time ? 1 : -1
      //   })
      // } else {
      //   for (let i = 0; i < this.finished.length; i++) {
      //     if (this.finished[i].id === item.id) {
      //       this.finished[i].switch = false
      //       this.Notfinished.push(this.finished[i])
      //       this.finished.splice(i, 1)
      //       // this. Notfinished[i].switch = false
      //     }
      //   }
      // }
      console.log(this.Notfinished)
      console.log(this.finished)
    },
    // 鼠标悬停
    enter (item) {
      this.$set(item, 'flag', true)
    },
    // 鼠标移开
    leave (item) {
      this.$set(item, 'flag', false)
      this.$set(item, 'isShow', false)
    },
    // 点击回车
    close (item) {
      this.$set(item, 'isShow', false)
    },
    // 修改
    amend (item) {
      this.$set(item, 'isShow', true)
      this.Notfinished.value = this.$refs.spaninput.innerHTML
      this.close()
      // this.isShow = 'true'
    }
  }
}

</script>
<style lang="less">
.head {
  width: 550px;
  height: 600px;
  background-color: #fff;
  margin: auto;
  padding-left: 25px;
  padding-top: 10px;
}

.title {
  padding-top: 20px;
  height: 50px;
  font-size: 18px;
  box-sizing: border-box;
}
.food {
  height: 800px;
  // overflow: auto;
  overflow-y: auto;
  // display: none;
  // scrou-y: auto;
}
.todo {
  display: flex;
  position: relative;
  width: 500px;
  height: 30px;
  background-color: #fff;
  // margin-top: 10px;
  padding-bottom: 15px;
  // border-bottom: 1px solid #ccc;
  .inputNotfinished {
    width: 498px;
    height: 41px;
    font-size: 12px;
    padding-left: 25px;
    border: none;
    line-height: 41px;
    // text-align: center;
    box-sizing: border-box;
    margin-top: 3px;
    // z-index: 1;
  }
  span:focus {
    outline: none;
  }
  p {
    position: absolute;
    top: 3px;
    right: 35px;
    font-size: 12px;
  }
}
.inputNotfinished:hover {
  background: #f0f0f0;
  // background-color: #000;
}
i:hover {
  background: rgba(0, 0, 0, 0.15);
}
// .todo:hover {
//   background: #f0f0f0;
// }
.line-through {
  // color: rgba(255, 255, 255, 0.5);
  text-decoration: line-through rgba(255, 255, 255, 0.8);
}
.form-onlyRead {
  pointer-events: none;
}
.disnone {
  display: none;
}
.checkbox {
  position: absolute;
  top: 13px;
  left: 0;
  cursor: pointer;
  // z-index: 1;
}
.add {
  font-size: 13px;
  color: #656565;
  margin-top: 10px;
  // cursor: pointer;
}
i {
  position: absolute;
  top: 17px;
  right: 10px;
  display: none;
  cursor: pointer;
  // height: 20px;
}
::-webkit-scrollbar {
  /*隐藏滚轮*/
  display: none;
}
</style>
