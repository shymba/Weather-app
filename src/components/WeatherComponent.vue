<template>
  <div class="container">
    <div class="container__inner">

      <label>Select the City</label>
      <div class="select-container">
        <select name="city" id="city" @change="onChange($event)">
          <option value="Kyiv">Kyiv</option>
          <option value="Vaterland">Vaterland</option>
          <option value="Sydney">Sydney</option>
          <option value="Porto">Porto</option>
          <option value="Tokyo">Tokyo</option>
          <option value="Orihuela">Orihuela</option>
          <option value="Alicante">Alicante</option>
          <option value="Valencia">Valencia</option>
          <option value="Madrid">Madrid</option>
          <option value="Murcia">Murcia</option>
          <option value="Torrevieja">Torrevieja</option>
        </select>
        <button class="btn" @click="addCity">Add City</button>
      </div>

      <div class="table">
        <table id="weather">
          <tr>
            <th>City</th>
            <th>MaxTemp</th>
            <th>MinTemp</th>
          </tr>
          <tr class="content" v-for="city in cities" :key="city" v-if="cities.length > 0">
            <td>{{ city.name }}</td>
            <td>{{ city.maxT }}</td>
            <td>{{ city.minT }}</td>
            <span class="trash" @click="removeCity(city.id)"><i class="far fa-trash-alt"></i></span>
          </tr>
          <p class="text" v-else>*The list is empty, please select a city from the dropdown list</p>
        </table>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      tempMax: null,
      tempMin: null,
      cities: [],
      city: 'Kyiv',
      id: 'Kyiv'
    }
  },
  methods: {
    onChange(e) {
      this.city = e.target.value;
      this.id = e.target.value;
    },
    addCity() {
      let citiesList = [];
      this.cities.forEach(name => citiesList.push(name.id))
      if(!citiesList.includes(this.city)) {
        this.getCityTemperature(this.city)
      }
    },

    async getCityTemperature(cityName) {
      const urlGeo = `https://geocoding-api.open-meteo.com/v1/search?name=${cityName}&count=1`;
      let timezone = null;
      let longitude = null;
      let latitude = null;
      let temp1 = null;
      let temp2 = null;

      const responseGeo = await fetch(urlGeo).then(res => res.json()).then(data => data.results[0]);
      timezone = responseGeo.timezone;
      longitude = responseGeo.longitude;
      latitude = responseGeo.latitude;

      const urlTemp = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&timezone=${timezone}&daily=temperature_2m_max,temperature_2m_min`;
      const responseTemp = await fetch(urlTemp).then(res => res.json()).then(data => data.daily);
      temp1 = responseTemp.temperature_2m_max;
      temp2 = responseTemp.temperature_2m_min;

      this.tempMax = Math.max(...temp1);
      this.tempMin = Math.min(...temp2);

      this.cities.push({name: this.city, maxT: this.tempMax, minT: this.tempMin, id: this.id})
    },
    removeCity(id) {
      this.cities = this.cities.filter(city => city.id !== id)
    },
  },
  async mounted() {
    await this.getCityTemperature(this.city);
  }
}
</script>

<style scoped lang="scss">
.container {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  margin: 0 auto;

  .container__inner {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;

    .table {
      width: 100%;
      border-radius: 5px;
    }

    #weather {
      border-collapse: collapse;
      width: 100%;
      color: #FFFFFF;
    }

    #weather td, #weather th {
      border: none;
      padding: 8px;
      color: #6D6C58;
    }

    #weather tr:nth-child(even) {
      background-color: #39343B;
    }

    #weather tr:hover td, tr:focus td {
      color: #fdd114;
    }

    #weather th {
      padding-top: 12px;
      padding-bottom: 12px;
      text-align: center;
      color: #FFFFFF;
    }

    .select-container {
      display: flex;
      justify-content: center;
      align-items: baseline;
    }

    label {
      color: #FFFFFF;
      margin-bottom: 20px;
    }

    .text {
      width: 100%;
      margin-top: 20px;
      margin-left: 100px;
    }

    .trash {
      i {
        margin-top: 8px;
        cursor: pointer;
      }

      i:hover {
        color: #CC0022;
        transform: scale(1.4);
      }
    }

    select {
      border: none;
      padding: 10px 20px;
      border-radius: 5px;
      margin-bottom: 20px;
      font-size: 16px;

      option {
        font-size: 14px;
      }
    }

    select:focus {
      outline: none;
      color: #fdd114;
      background-color: #6D6C58;
    }

    .btn {
      font-size: 16px;
      padding: 5px 8px;
      outline: none;
      background-color: transparent;
      color: #fdd114;
      border: none;
      margin-left: 15px;
      cursor: pointer;
    }
  }
}

</style>
