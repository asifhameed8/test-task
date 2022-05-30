<template>
  <div>
    <h1>Test task</h1>
    <div>
      <input type="text" v-model="name" placeholder="name" />
      <input type="text" v-model="supervisorID" placeholder="supervisor id" />
      <button @click="add()">Add</button>
    </div>
    <div class="move-wrapper">
      <input type="text" v-model="mEmployeeID" placeholder="Employee Id" />
      <input type="text" v-model="mSupervisorID" placeholder="Supervisor Id" />
      <button @click="move(mEmployeeID, mSupervisorID)">Move</button>
    </div>
    <center class="action-btn">
      <div>
        <button @click="undo()">Undo</button>
        <button class="redo-btn" @click="redo()">Redo</button>
      </div>
    </center>
    <div>
      <div v-for="(item, key) in employees" :key="key">
        <h3>Id: {{ item.uniqueId }} Name: {{ item.name }}</h3>
        <div style="margin-left: 20px" v-for="(inItem, key) in filteredEmployee(item)" :key="key">
          <p>Id: {{ inItem.uniqueId }} Name: {{ inItem.name }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'Home',
  components: {
  },
  data() {
    return {
      history: [],
      redoState: [],
      popState: [],
      name: '',
      supervisorID: '',
      mSupervisorID: '',
      mEmployeeID: '',
      employees: [
        {
          uniqueId: 1212,
          name: 'Mark Zuckerberg',
          supervisorID : null
        }
      ],
    }
  },
  methods: {
    filteredEmployee(item) {
      return  this.employees.filter((e) => (e.supervisorID === item.uniqueId) && (item.uniqueId !== e.uniqueId))
    },
    generateRandom() {
      return Math.floor(Math.random() * 10000) + 1;
    },
    add() {
      const newEmployee = {
        uniqueId:  this.generateRandom(),
        name: this.name,
        supervisorID: parseInt(this.supervisorID)
      }
      this.employees = [...this.employees, newEmployee]
      this.name = ''
      this.supervisorID = ''
      this.history.push({name: 'add', newEmployee  })
    },
    addRedo(obj) {
      this.employees = [...this.employees, obj.newEmployee]
    },
    remove(id) {
      let filteredEmployees = this.employees.filter((e) => e.uniqueId !== id)
      this.employees = [...filteredEmployees]
    },
    undo() {
      if(this.history.length) {
        const obj = this.history.pop()
        if(obj.name === 'move') {
          this.move(obj.mEmployeeID, obj.preSupervisor)
          this.redoState.push(obj)
        }
        if(obj.name === 'add') {
          this.remove(obj.newEmployee.uniqueId)
          this.redoState.push(obj)
        }
      }
    },
    redo() {
      if(this.redoState.length) {
        const obj = this.redoState.pop()
        if(obj.name === 'move') {
          this.move(obj.mEmployeeID, obj.preSupervisor)
        }
        if(obj.name === 'add') {
          this.addRedo(obj)
        }
      }
    },
    move(mEmployeeID, mSupervisorID) {
      let employee = this.employees.find((e) => e.uniqueId === parseInt(mEmployeeID))
      let preSupervisor = employee.supervisorID
      let newEmployeesSupervisor = []
      let newEmployeesSupervisorId = [...mEmployeeID]
      let childEmployee = this.employees.filter((e) => e.supervisorID === employee.uniqueId)
      childEmployee.forEach((e) => {
        newEmployeesSupervisorId.push(e.uniqueId)
        newEmployeesSupervisor.push({
          uniqueId: e.uniqueId,
          name: e.name,
          supervisorID : employee.supervisorID
        })
      })
      employee.supervisorID = parseInt(mSupervisorID)
      let latestArray  = this.employees.filter(value => !newEmployeesSupervisorId.includes(value.uniqueId));
      let arr = [...latestArray, ...newEmployeesSupervisor, employee]
      const result = [...new Set(arr.map(a => JSON.stringify(a)))].map(a => JSON.parse(a))
      this.employees = [...result]
      this.history.push({name: 'move', mEmployeeID, preSupervisor})
      this.mEmployeeID = ''
      this.mSupervisorID = ''
    }
  }
}
</script>

<style scoped>
input {
  margin-right: 5px;
  height: 22px;
  border: 1px solid teal;
  border-radius: 5px;
}
.move-wrapper {
  margin-top: 10px;
}

button {
  background-color: teal;
  outline: none;
  border: none;
  height: 26px;
  border-radius: 5px;
  color: white;
  width: 50;
  cursor: pointer;
}

.action-btn {
  margin-top: 10px;
  width: 100%;
}

.redo-btn {
  margin-left: 10px;
}

</style>
