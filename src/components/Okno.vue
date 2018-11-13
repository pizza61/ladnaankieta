<template>
  <div class="okno">
    <div class="tytul">
      <span class="grubas">{{naglowek}}</span>
      <span class="niegrubas">{{podnaglowek}}</span>
    </div>
    <div v-if="!skonczone">
    <div class="reszta">
      {{ current.pytanie }}
      <input v-bind:placeholder="current.placeholder" v-model="current.odpowiedz" class="input" v-if="current.type == 'input'">
      <div v-else-if="current.type == 'yn'">
        <div class="yn" 
          @click="current.odpowiedz = 'Tak'"
          :class="current.odpowiedz == 'Tak' ? 'selected' : ''"
          >Tak</div>
        <div class="yn"
          @click="current.odpowiedz = 'Nie'"
          :class="current.odpowiedz == 'Nie' ? 'selected' : ''"
        >Nie</div>
      </div>
      <div v-else-if="current.type == 'select'">
        <div class="yn"
        v-for="(opcja, index) in current.wybor"
        :key="index"
        @click="current.odpowiedz = opcja"
        :class="current.odpowiedz == opcja ? 'selected' : ''">{{ opcja }}</div>
      </div>
    </div>
    <div class="stopek">
      <div class="kropeczki">
        <i v-for="pytanie in pytanka" :key="pytanie.id" class="kropeczka" :class="g(pytanie)"></i>
      </div>
      <span style="flex: 1 100%"></span>
      <button class="btn" :disabled="current.id == 0" v-on:click="wstecz()">Wstecz</button>
      <button class="btn" :disabled="!current.odpowiedz" v-on:click="nastepnePytanko()">{{ last() }}</button>
    </div>
    <div class="blad" v-if="blad">
      Wystąpił błąd, zobacz konsolę po więcej informacji
    </div>
    </div>
    <div v-else class="podziekowanie">
      <span>{{ pozegnalna }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Okno',
  data: function() { return {
    // NIE RUSZAĆ
    skonczone: false,
    current: {},
    blad: false,

    // do ustawienia
    naglowek: "Czy lubisz zakupy w naszym sklepie?",
    podnaglowek: "Opowiedz nam o swoich doświadczeniach",
    pozegnalna: "Sklep Real dziękuje za wypełnienie ankiety!",
    pytanka: [{
      id: 0,
      pytanie: "Co najbardziej podoba się państwu w naszym sklepie?",
      placeholder: "xd",
      type: "input"
    },{
      id: 1,
      pytanie: "Czy polecił(a)byś nasz sklep swojemu znajomemu?",
      type: "yn",
      odpowiedz: '',
    },{
      id: 2,
      pytanie: "Który z wymienionych działów, według ciebie, jest najciekawszy?",
      type: "select",
      wybor: ["Elektronika", "Pieczywo", "Ryby", "Ryby"],
      odpowiedz: '',
    }],
  }},
  beforeMount: function() {
    this.pytanka.forEach((dat, i) => {
      if(dat.id != 0 && !dat.id) {
        this.wywolajBlad(i, "Nie ma ID")
      }
      switch(dat.type) {
        case 'input':
          break;
        case 'yn':
          break;
        case 'select':
          if(!dat.wybor) {
            this.wywolajBlad(i, "Brak opcji do wyboru")
          }
          break;
        default:
          this.wywolajBlad(i, "Nie ma określonego typu")
          break;
      }
      
    })
    this.current = this.pytanka[0];
  },
  methods: {
    g: function(pytanie) {
      if (pytanie.pytanie == this.current.pytanie) {
        return 'aktualna'
      } else {
        return ''
      }
    },
    nastepnePytanko: function() {
      this.pytanka[this.current.id] = this.current;
      if(this.pytanka[this.current.id+1]) {
        this.current = this.pytanka[this.current.id+1]
      } else {
        this.skonczone = true;
        this.pytanka.forEach((x, i) => {
          console.log(`${x.id}: ${x.pytanie} - ${x.odpowiedz}`)
        })
      }
    },
    wstecz: function() {
      if(this.pytanka[this.current.id-1]) {
        this.current = this.pytanka[this.current.id-1]
      } else {
        console.log("jak ty to zrobiłeś?")
      }
    },
    last: function() {
      if(this.pytanka[this.current.id+1]) {
        return 'Kontynuuj'
      } else {
        return 'Wyślij'
      }
    },
    wywolajBlad: function(i, x) {
      console.error("Pytanie "+i+": "+x)
      this.blad = true;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@media screen and (min-width: 1024px) {
  .okno {
    width: 50%;
  }
}
.okno {
  box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
  margin: auto;
}
.tytul {
  padding: 20px;
  color: white;
  background: linear-gradient(90deg, #0d8ac2 0%, #188e9b 100%);
}
.grubas {
  padding-bottom: 20px;
  display: block;
  font-weight: 600;
  font-size: 115%;
}

.niegrubas {
  display: block;
  font-size: 100%;
}

.reszta {
  padding: 20px;
}

.blad {
  background: #d5333d;
  color: white;
  font-weight: 300;
  padding: 20px;
}
.podziekowanie {
  padding: 20px;
}

.input {
  outline: none;
  border: 2px solid #bfbfbf;
  
  background: white;
  display: block;
  margin-top: 10px;
  //margin: 10px 0;
  width: 80%;
  padding: 5px 8px;
  //color: #2c3e50;
  font-family: 'PT Sans', sans-serif;
  font-weight: 500;
  font-size: 90%;
  //letter-spacing: 0.3px;
  color: #404040;
  border-radius: 3px;
  
}
.input:focus {
  box-shadow: 0 0 3px 1px #0073b1;
  border-color: #0073b1;
}

.yn {
  display: inline-block;
  margin-top: 10px;
  user-select: none;
  cursor: pointer;
  border-radius: 3px;
  padding: 8px 20px;
  font-weight: 600;
  font-size: 100%;
  transition: 200ms;

  &:hover {
    background: rgba(0,115,177, 0.8);
    color: white;
  }
}
.selected {
  background: #0073b1;
  color: white;
}
.stopek {
  display: flex;
  align-items: center;
  padding: 20px;
  border-top: 1px solid #bfbfbf;
}

.btn {
  margin-left: 10px;
  background: #0073b1;
  border: none;
  border-radius: 3px;
  color: white;
  font-weight: 600;
  font-size: 90%;
  padding: 8px 20px;
  transition: 200ms;

  &:hover {
    background: rgba(0,115,177, 0.8);  
  }

  &:disabled {
    background: rgba(0,115,177, 0.3); 
  }
}

.kropeczki {
  display: flex;
}
.kropeczka {
  margin-right: 10px;
  background: #bfbfbf;
  width: 8px;
  height: 8px;
  border-radius: 2138px;
  display: inline-block;
}
.aktualna {
  background: #404040;
}
</style>
