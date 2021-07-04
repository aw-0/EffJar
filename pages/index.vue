<template>
  <div class="p-7 h-screen justify-center items-center">
    <div class="mx-auto font-semibold">
      <h1 class="font-extrabold text-2xl">The Eff Jar</h1>
      <h3 class="mt-5 font-semibold underline text-lg">Leaderboard</h3>
      <div class="mb-3" v-for="eff in effData" :key="eff.id">
        <div class="flex">
          <p>{{eff.id}} owes ${{eff.data.howMuch}}</p>
        </div>
      </div>
      <div class="mt-5 max-w-md">
        <h3 class="font-semibold underline text-lg">Add to The Jar</h3>
        <p>Who?</p>
        <input name="field_name" class="border border-2 rounded-r px-4 py-2 w-full" type="text" v-model="who" />
        <p>They owe: </p>
        <div class="flex">
            <span class="text-sm border border-2 rounded-l px-4 py-2 bg-gray-300 whitespace-no-wrap">$</span>
            <input name="field_name" class="border border-2 rounded-r px-4 py-2 w-full" type="number" v-model="howMuch" />
        </div>
        <button
            type="button"
            v-on:click="submit()"
            class="border border-indigo-500 bg-indigo-500 text-white rounded-md px-4 py-2 mr-2 mt-2 transition duration-500 ease select-none hover:bg-indigo-600 focus:outline-none focus:shadow-outline">
            Submit
          </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      who: '',
      howMuch: 0.10,
      effData: []
    }
  },
  async mounted(){
    const mountedData = await this.$axios.$post('https://us-central1-axiliquint-dev.cloudfunctions.net/effJar',{
      headers: {
          'Content-Type': 'application/json'
        },
      type: 'get',
    })
    console.log(mountedData.data)
    this.effData = mountedData.data
  },
  methods:{
    async submit(){
      const submitData = await this.$axios.$post('https://us-central1-axiliquint-dev.cloudfunctions.net/effJar', {
        headers: {
          'Content-Type': 'application/json'
        },
          type: 'submit',
          name: this.who.toLowerCase(),
          owed: this.howMuch
      })
      const index = this.effData.findIndex((data) => {
        return data.id == this.who.toLowerCase()
      })
      console.log(index)
      if (index >= 0){
        console.log(this.effData[index].data.howMuch)
        const howMuch = parseFloat(this.effData[index].data.howMuch)
        console.log(howMuch)
        const howMuchForm = parseFloat(this.howMuch)
        this.effData[index].data.howMuch = howMuch + howMuchForm
      } else {
        this.effData.push({
          id: this.who,
          data: {
            howMuch: this.howMuch
          }
        })
      }
    }
  },
}
</script>

<style>
</style>
