<template>
  <div id="app">
    <div class="team-container">
      <div class="team-container-header">
        <span>EQUIPOS</span> <button class="button-success pure-button" @click='addItemView = !addItemView'>+</button>
        <div v-show="addItemView">
          <input class="team-new-Item" v-model="newItem" @keyup.enter="addItem()" placeholder="Nombre Equipo" />
          <button  @click.prevent="(editView ? editItem:addItem)()" class="pure-button pure-button-primary">{{ editView ? 'Editar' : 'Agregar'}}</button>
        </div>
      </div>
      <ul>
        <li
          draggable="true"
          v-for="(item, index) in teamItems"
          :id="`team_${index}`"
          v-on:dragstart="dragstart(item, $event)"
        >
          <div>{{ item }}</div>
          <div>
            <button  @click.prevent="showEditItem(index)" class="pure-button button-warning">E</button>
            <button  @click.prevent="remove(index)" class="pure-button button-error">x</button>
          </div>
        </li>
      </ul>
    </div>
    <div class="match-container">
      <div class="match-contanier_define">
        <div @dragover.prevent @drop="localTeam" v-text="nameLocalTeam"></div>
        <div>
          <datepicker
            format="dd/MM/yyyy"
            placeholder="Selecciona una fecha"
            v-model="dateMatch"></datepicker>
        </div>
        <div @dragover.prevent @drop="visitTeam" v-text="nameVisitTeam"></div>
      </div>
      <div>  <button class="pure-button pure-button-primary" @click='addMatch'> Agregar encuentro </button></div>
      <div class="match-container-list" v-for="(item, index) in matchList">
        <div>
          {{ item.local }}
        </div>
        <div>
          {{ item.fecha }}
        </div>
        <div>
          {{ item.visitante }}
        </div>
        <div>
          <button  @click.prevent="removeMatch(index)" class="pure-button button-error">x</button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker';

export default {
  name: 'app',
  components: {
    Datepicker
  },
  data() {
    return {
      matchList: [],
      teamItems: ['equipo 1', 'equipo 2'],
      newItem: '',
      addItemView: false,
      editView: false,
      itemToEdit: -1,
      draggableItem: '',
      nameLocalTeam: 'Local',
      nameVisitTeam: 'Visitante',
      dateMatch: ''
    }
  },
  methods: {
    cleanDate(dirtyDate) {
      const options = { day: '2-digit', month: '2-digit', year: 'numeric' }
      const splitFullDate = dirtyDate.toLocaleString('es-MX', options).split(' ')
      return splitFullDate[0].replace(/\//g, '/')
    },
    addMatch() {
      const localTeam = this.teamItems.indexOf(this.nameLocalTeam)
      const visitTeam = this.teamItems.indexOf(this.nameVisitTeam)
      const currentDate = new Date().toLocaleDateString('es-MX')
      const dateCurrentMatch = this.cleanDate(this.dateMatch)
      if(
        (Date.parse(dateCurrentMatch) >= Date.parse(currentDate))
        && localTeam >= 0
        && visitTeam >=0
        && localTeam !== visitTeam
      ) {
        this.matchList.push(
          {
            local: this.teamItems[localTeam],
            fecha: dateCurrentMatch,
            visitante: this.teamItems[visitTeam]
          }
        )
        this.nameLocalTeam = 'Local'
        this.nameVisitTeam = 'Visitante'
      }
    },
    dragstart(item, e) {
      this.draggableItem = item
      this.draggingItem = item
    },
    localTeam(e) {
      if(this.nameVisitTeam !== this.draggableItem) this.nameLocalTeam = this.draggableItem
    },
    visitTeam(e) {
      if(this.nameLocalTeam !== this.draggableItem) this.nameVisitTeam = this.draggableItem
    },
    remove(index) {
      this.teamItems.splice(index, 1)
      this.itemToEdit= -1
      this.addItemView = false
      this.editView = false
      this.newItem = ''
    },
    removeMatch(index) {
      this.matchList.splice(index, 1)
      this.nameLocalTeam = 'Local'
      this.nameVisitTeam = 'Visitante'
    },
    addItem() {
      if(this.newItem.length > 0 && this.teamItems.indexOf(this.newItem) < 0) {
        this.teamItems.push(this.newItem)
        this.newItem = ''
      }
    },
    showEditItem(index) {
      this.itemToEdit= index
      this.addItemView = true
      this.editView = true
      this.newItem = this.teamItems[index]
    },
    editItem() {
      if(this.newItem.length > 0 && this.teamItems.indexOf(this.newItem) < 0) {
        if(this.nameLocalTeam === this.teamItems[this.itemToEdit]) {
          this.nameLocalTeam = this.newItem
        }
        if(this.nameVisitTeam === this.teamItems[this.itemToEdit]) {
          this.nameVisitTeam = this.newItem
        }
        this.teamItems[this.itemToEdit] = this.newItem
        this.itemToEdit= -1
        this.addItemView = false
        this.editView = false
        this.newItem = ''
      }
    }
  }
}
</script>

<style lang="scss">
body {
  padding: 1rem;
  margin: 0 auto;
  max-width: 60rem;

  #app {
    display: flex;

    > div {
      margin: 1rem;
      padding: 1rem;
      border-radius: 5px;
      border: 1px solid #000;
    }

    .team-container {
      width: 30%;

      ul {
        list-style: none;
        margin: 0;
        padding: 0;

        li {
          display: flex;
          justify-content: space-between;
          border: 1px solid #ccc;
          margin-top: 1rem;
          padding: 1rem;
          cursor: pointer;
        }
      }

      .team-container-header {
        margin-bottom: 1rem;
        text-align: center;

        > div {
          margin-top: 1rem;
        }

        .team-new-Item {
          width: 8rem;
          padding: 5px;
        }
      }
    }

    .match-container {
      width: 70%;

      .match-container-list {
        display: flex;
        justify-content: space-between;
      }

      > div {
        margin: 1rem;
        padding: 1rem;
        border-radius: 5px;
        border: 1px solid #000;

      }

      .match-contanier_define {
        display: flex;
        text-align: center;
        justify-content: space-between;

        > div {
          width: 30%;
        }

      }
    }
  }
}
</style>
