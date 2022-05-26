<template>
  <div>
    <h1>Test task</h1>
    <div>
      <input type="text" v-model="name" placeholder="name" />
      <input type="text" v-model="supervisorID" placeholder="supervisor id" />
      <button @click="add()">Add</button>
    </div>
    <div>
      <input type="text" v-model="mEmployeeID" placeholder="Employee Id" />
      <input type="text" v-model="mSupervisorID" placeholder="Supervisor Id" />
      <button @click="move();">Move</button>
    </div>
    <div class="">
      <div v-for="(item, key) in employees" :key="key">
        <h3>Id: {{item.uniqueId}} Name: {{ item.name }}</h3>
        <div style="margin-left: 20px" v-for="(inItem, key) in employees.filter((e) => (e.supervisorID === item.uniqueId) && (item.uniqueId !== e.uniqueId))" :key="key">
          <p>Id: {{inItem.uniqueId}} Name: {{ inItem.name }}</p>
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
    },
    setHistory() {
      
    },
    undo() {

    },
    redo() {

    },
    move() {
      let employee = this.employees.find((e) => e.uniqueId === parseInt(this.mEmployeeID))     
      let newEmployeesSupervisor = []
      let newEmployeesSupervisorId = [...this.mEmployeeID]
      let childEmployee = this.employees.filter((e) => e.supervisorID === employee.uniqueId)
      childEmployee.forEach((e) => {
        newEmployeesSupervisorId.push(e.uniqueId)
        newEmployeesSupervisor.push({
          uniqueId: e.uniqueId,
          name: e.name,
          supervisorID : employee.supervisorID
        })
      })
      employee.supervisorID = parseInt(this.mSupervisorID)
      let latestArray  = this.employees.filter(value => !newEmployeesSupervisorId.includes(value.uniqueId));
      this.employees = [...latestArray, ...newEmployeesSupervisor, employee]

      this.mEmployeeID = ''
      this.mSupervisorID = ''
    }
  }
}
</script>
