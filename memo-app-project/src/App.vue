<template>
  <div>
    <h2 v-if="isNewMemo">メモ新規追加</h2>
    <h2 v-else-if="isEdit">メモ編集</h2>
    <h2 v-else>メモ一覧</h2>
    <ul>
    <li v-for="(memo, index) in memos" :key="memo.id">
      <a href="javascript:void(0)" @click="edit(index)">{{ fetchTitle(memo) }}</a>
    </li>
    </ul>
    <div v-if="isEdit">
      <textarea rows="5" cols="33" v-model="content"></textarea>
      <button @click="update">更新</button>
      <button @click="remove">削除</button>
    </div>
    <p v-if="!isNewMemo && !isEdit" @click="create">➕</p>
    <div v-if="isNewMemo" >
      <textarea rows="5" cols="33" v-model="content"></textarea>
      <button @click="add">追加</button>
      <button @click="back">戻る</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      content: '',
      isEdit: false,
      editIndex: '',
      isNewMemo: false,
      memos: []
    }
  },
  mounted () {
    if (localStorage.getItem('memos')) {
      try {
        this.memos = JSON.parse(localStorage.getItem('memos'))
      } catch (e) {
        localStorage.removeItem('memos')
      }
    }
  },
  methods: {
    create () {
      this.isNewMemo = true
    },
    back () {
      this.isNewMemo = false
    },
    add () {
      const max = this.memos.reduce(function (a, b) {
        return a.id > b.id ? a.id : b.id
      }, 0)
      this.memos.push({
        id: max + 1,
        content: this.content
      })
      this.save()
      this.content = ''
      this.isNewMemo = false
    },
    edit (index) {
      this.content = this.memos[index].content
      this.isEdit = true
      this.isNewMemo = false
      this.editIndex = index
    },
    update () {
      this.memos.splice(this.editIndex, 1, {
        id: this.memos[this.editIndex].id,
        content: this.content
      })
      this.save()
      this.isEdit = false
      this.content = ''
    },
    remove () {
      this.memos.splice(this.editIndex,1)
      this.save()
      this.isEdit = false
      this.content = ''
    },
    save () {
      const parsed = JSON.stringify(this.memos)
      localStorage.setItem('memos', parsed)
    }
  },
  computed: {
    fetchTitle () {
      return memo => memo.content.split(/\n/)[0]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
}

ul {
  list-style-type: none;
}

textarea {
    padding: 10px;
    line-height: 1.5;
    border-radius: 5px;
    border: 1px solid #ccc;
}

button {
  display: inline-block;
  padding: 7px 20px;
  margin: 0.3rem;
  border-radius: 0.3rem;
  text-decoration: none;
  color: black;
  transition: .4s;
}

button:hover {
  background-color: greenyellow;
}
</style>
