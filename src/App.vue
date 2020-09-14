<template>
  <div id="app" class="container">
    <button v-if="active == 'slap'" @click="active = 'miner'">Miner</button>
    <button v-else @click="active = 'slap'">Slap</button>
    <div v-if="active == 'slap'">
      <div class="row">
        <div class="card col-4" v-for="target in opponents" :key="target.name">
          <img :class="{dead: target.health < 1}" :src="target.imgUrl" />
          <div class="card-body">
            <h4
              :class="{'text-warning': target.health < 50 && target.health > 9, 'text-danger': target.health < 10}"
              class="card-title"
            >{{target.name}}</h4>
            <p class="card-text">Health: {{target.health}}</p>
            <div v-if="target.health > 0">
              <button type="button" class="btn btn-danger" @click="attack(1, target)">Slap</button>
              <button type="button" class="btn btn-danger" @click="attack(5, target)">Punch</button>
              <button type="button" class="btn btn-danger" @click="attack(10, target)">Kick</button>
            </div>
            <div v-else>
              <button type="button" class="btn btn-success" @click="target.health=100">Reset</button>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col">
          <h1>New Opponent</h1>
          <form @submit.prevent="createOpponent">
            <div class="form-group">
              <label for="name">Name</label>
              <input
                type="text"
                id="name"
                class="form-control"
                placeholder="NAME"
                v-model="newOpponent.name"
                aria-describedby="helpId"
              />
            </div>
            <div class="form-group">
              <label for="hp">HP</label>
              <input
                type="number"
                id="hp"
                class="form-control"
                aria-describedby="helpId"
                v-model="newOpponent.health"
              />
            </div>
            <div class="form-group">
              <label for="imgUrl">Image URL</label>
              <input
                type="text"
                id="imgUrl"
                class="form-control"
                placeholder="IMG URL"
                v-model="newOpponent.imgUrl"
                aria-describedby="helpId"
              />
            </div>
            <button type="submit" class="btn btn-success">Create</button>
          </form>
        </div>
      </div>
    </div>

    <div v-else>
      <img src="https://pngimg.com/uploads/moon/moon_PNG20.png" height="300px" @click="mine" />
      <h3>Cheese: {{cheese}}</h3>
      <button
        v-for="item in upgrades"
        :key="item.name"
        @click="buy(item)"
      >{{item.name}} ${{item.cost}} (quantity: {{item.quantity}})</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  mounted() {
    let opps = localStorage.getItem('opponents')
    if (opps) {
      this.opponents = JSON.parse(opps);
    }
    this.save()
    setInterval(() => {
      this.autoMine()
    }, 3000);
  },
  data() {
    return {
      cheese: 0,
      upgrades: {
        pick: {
          name: "Cheese Pick",
          cost: 10,
          multiplier: 1,
          quantity: 0,
          click: true
        },
        cart: {
          name: "Cheese Cart",
          cost: 100,
          multiplier: 10,
          quantity: 0,
          click: true
        },
        rover: {
          name: "Rover",
          cost: 100,
          multiplier: 10,
          quantity: 0
        }
      },

      active: "miner",
      newOpponent: {
        name: '',
        health: 0,
        imgUrl: ''
      },
      opponents: [
        {
          name: "Scarecrow",
          health: 100,
          imgUrl: "https://thumbs.gfycat.com/PastCookedCuscus-size_restricted.gif"
        },
        {
          name: "Penguin",
          health: 70,
          imgUrl: "https://media.tenor.com/images/7e85641b82c163554091b5158da0116e/tenor.gif"
        },
        {
          name: "Joker",
          health: 110,
          imgUrl: "https://www.animatedimages.org/data/media/154/animated-clown-image-0295.gif"
        },
      ]
    }
  },
  methods: {
    mine() {
      this.cheese++
      this.cheese += this.clickModifiersTotal
    },

    autoMine() {
      this.cheese += this.autoModifiersTotal
    },

    buy(item) {
      if (item.cost < this.cheese) {
        this.cheese -= item.cost
        item.quantity++
        item.cost *= 2
      }
    },

    attack(dmg, target) {
      target.health -= dmg
      if (target.health < 0) {
        target.health = 0
      }
    },
    createOpponent() {
      this.opponents.push(this.newOpponent)
      this.newOpponent = {
        name: '',
        health: 0,
        imgUrl: ''
      }
      this.save();
    },
    save() {
      localStorage.setItem('opponents', JSON.stringify(this.opponents))
    }
  },
  computed: {
    clickModifiersTotal() {
      let total = 0
      for (let key in this.upgrades) {
        let upgrade = this.upgrades[key]
        if (upgrade.click) {
          total += upgrade.multiplier * upgrade.quantity
        }
      }
      return total
    },
    autoModifiersTotal() {
      let total = 0
      for (let key in this.upgrades) {
        let upgrade = this.upgrades[key]
        if (!upgrade.click) {
          total += upgrade.multiplier * upgrade.quantity
        }
      }
      return total
    }
  }
}
</script>


<style scoped>
img {
  transition: 1s ease;
}
/* img:hover {
  transform: scale(1.1);
} */

.dead {
  transform: rotate(90deg);
}
</style>