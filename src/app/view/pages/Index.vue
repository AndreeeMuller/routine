<template>
  <q-page class="text-black">
    <q-page-sticky position="bottom-right" :offset="[18, 18]">
      <q-btn
        fab
        icon="add"
        color="teal-5"
        to="/task"
      />
    </q-page-sticky>
    <q-scroll-area
      horizontal
      style="width: 100%;"
      class="secondary-bg-color rounded-borders"
    >
      <div class="row no-wrap">
        <q-btn
          flat
          size="md"
          color="white"
          style="margin: 3% 0% 3% 4%"
          label="ALL"
          stack
          @click="loadTasks()"
        />
        <q-btn
          flat
          v-for="item in groups" :key="item.id"
          size="md"
          style="margin: 3% 0%"
          :color="item.color"
          :label="item.name"
          stack
          @click="loadTasksByIdGroup(item.id)"
        />
      </div>
    </q-scroll-area>
    <q-scroll-area
      class="list-main secondary-bg-color rounded-borders"
    >
      <list-action-component
        style="margin: 0% 5%"
        section="name"
        use-index-form="true"
        :data="tasks"
        key-components="icon"
        @action="goToTask"
      />
    </q-scroll-area>
  </q-page>
</template>

<script>
import Database from '../../infrastructure/persistense/Database'
import Tables from '../../infrastructure/persistense/Tables'
import ListActionComponent from '../../infrastructure/view/components/list/ListActionComponent'
import GroupControllerBuilder from '../../infrastructure/builder/controller/GroupControllerBuilder'
import TaskControllerBuilder from '../../infrastructure/builder/controller/TaskControllerBuilder'
import IconListBuilder from '../../infrastructure/builder/forms/IconListBuilder'

export default {
  name: 'PageIndex',
  data () {
    return {
      colorButtons: [],
      color: '',
      tasks: [],
      groups: [],
      groupController: GroupControllerBuilder.findAll(),
      taskFindAllController: TaskControllerBuilder.findAll(),
      taskFindByGroupController: TaskControllerBuilder.findByGroup()
    }
  },
  components: {
    ListActionComponent
  },
  methods: {
    loadGroups () {
      this.groupController.findAll()
        .then(data => {
          this.groups = data
        })
    },
    loadTasks () {
      this.taskFindAllController.findAll()
        .then(data => {
          this.loadListItem(data)
        })
    },
    loadTasksByIdGroup (id) {
      this.taskFindByGroupController.findByIdGroup(id)
        .then(data => {
          this.loadListItem(data)
        })
    },
    loadListItem (data) {
      this.tasks = []

      data.forEach(item => {
        let iconListBuilder = new IconListBuilder()
        iconListBuilder.addIcon(item.color)

        this.tasks.push({
          created: item.created,
          name: item.name,
          id: item.id,
          icon: iconListBuilder.getFields(),
          action: 'goToTask'
        })
      })
    },
    createTableIfNotExists () {
      const database = Database.getConnection()
      const tables = new Tables(database)
      tables.createTable()
    },
    goToTask (item) {
      this.$router.push(`/task/${item.value.id}`)
    }
  },
  created () {
    this.createTableIfNotExists()
    this.loadGroups()
    this.loadTasks()
  }
}
</script>

<style>
  .list-main {
    width: 90%;
    margin-top: 15%;
    border-radius: 0% 2% 2% 0%;
    height: 24em
  }
</style>
