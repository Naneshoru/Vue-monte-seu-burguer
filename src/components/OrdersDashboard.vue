<template>
  <div class="burger-table">
    <InlineMessage :msg="msg" />
    <div>
      <div class="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente: </div>
        <div>Pão: </div>
        <div>Carne: </div>
        <div>Opcionais: </div>
        <div>Ações: </div>
      </div>
    </div>
    <div class="burger-table-rows" >
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul>
            <li v-for="(opcional, index) in burger.opcionais" :key="index">{{ opcional }}</li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updateBurger($event, burger.id)">
            <option v-for="sts in status" :key="sts.id" :value="sts.tipo" :selected="burger.status === sts.tipo">
              {{ sts.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
        </div>
      </div>
      
    </div>
  </div>
</template>

<script>
import InlineMessage from '../interface/InlineMessage.vue'

export default {
  name: 'OrdersDashboard',
  data() {
    return {
      burgers: [],
      burguerId: '',
      status: [],
      msg: ''
    }
  },
  components: {
    InlineMessage
  },
  methods: {
    async getOrders() {
      const req = await fetch('http://localhost:3000/burgers')
      const data = await req.json();
      this.burgers = data;

      this.getStatus()
    },
    async getStatus() {
      const req = await fetch('http://localhost:3000/status')
      const data = await req.json();
      this.status = data;
    },
    async deleteBurger(id) {
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: 'DELETE'
      })

      const res = await req.json();
      
      this.msg = `Pedido Nº ${res.id} removido com sucesso!`
      setTimeout(() => this.msg = '', 3000);

      this.getOrders();
    },
    async updateBurger(event, id) {
      const option = event.target.value;

      const dataJson = JSON.stringify({ status: option })

      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: 'PATCH',
        headers: {'Content-Type': 'application/json'}, 
        body: dataJson
      })

      const res = await req.json();
      
      this.msg = `Pedido Nº ${res.id} atualizado para '${res.status}'!`
      setTimeout(() => this.msg = '', 3000);
    }
  },
  mounted() {
    this.getOrders();
  }
}
</script>

<style scoped>
  .burger-table {
    max-width: 1200px;
    margin: 0 auto;
    width: 100%;
  }

  .burger-table-heading,
  .burger-table-rows,
  .burger-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  .burger-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  .burger-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #CCC;
  }

  .burger-table-heading div,
  .burger-table-row div {
    width: 19%;
  }

  .burger-table-heading .order-id,
  .burger-table-row .order-number {
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn {
    background-color: #222;
    color:var(--light-accent);
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }
  
  .delete-btn:hover {
    background-color: transparent;
    color: #222;
  }
  
</style>