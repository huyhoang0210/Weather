<template>
    <div id="app">
        <div class="container">
            <div class="search-bar">
                <font-awesome-icon icon="search" class="search-icon" />
                <input
                    type="text"
                    v-model="city"
                    class="search-input"
                    placeholder="Tìm kiếm"
                    @change="getAPI"
                />
            </div>

            <div>
                <div class="time">
                    <span>{{ time }}</span>
                </div>
                <div class="hour">
                    <span>{{ time1 }}</span>
                </div>
                <div v-if="data.data" class="address">{{ data.data.name }}</div>
                <div v-else class="address">- -</div>
            </div>

            <div>
                <img class="image" :src="srcImage" />
                <p class="temperature">{{ temperature(data) }}</p>
                <span class="current-day">{{ time2 }}</span>
                <p class="line"></p>
            </div>

            <div class="footer">
                <div class="footer-left">
                    <p class="font-bold">
                        MT Mọc: <span class="font-nomal"> {{ sunrise }} </span>
                    </p>
                    <p class="font-bold">
                        Độ ẩm: <span class="font-nomal"> {{ humidity }}%</span>
                    </p>
                </div>
                <div class="footer-right">
                    <p class="font-bold">
                        MT Lặn: <span class="font-nomal"> {{ sundown }} </span>
                    </p>
                    <p class="font-bold">
                        Gió: <span class="font-nomal"> {{ wind }} m/s </span>
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

const getData = (params) =>
    axios.get(`https://api.openweathermap.org/data/2.5/weather`, { params });

export default {
    name: "App",

    data: function() {
        return {
            city: "",
            data: "- -",
        };
    },

    computed: {
        srcImage() {
            if (this.data.data) {
                return `http://openweathermap.org/img/wn/${this.data.data.weather[0].icon}@2x.png`;
            }

            return "http://openweathermap.org/img/wn/10d@2x.png";
        },

        sunrise() {
            if (this.data.data) {
                const sunrise = this.data.data.sys.sunrise;

                return moment.unix(sunrise).format("H:mm");
            }

            return "6:16";
        },

        sundown() {
            if (this.data.data) {
                const sundown = this.data.data.sys.sunset;

                return moment.unix(sundown).format("H:mm");
            }

            return "6:00";
        },

        humidity() {
            if (this.data.data) {
                return this.data.data.main.humidity;
            }

            return "80";
        },

        wind() {
            if (this.data.data) {
                return ((this.data.data.wind.speed)*3.6).toFixed(2);
            }
            return "19.54";
        },

        time() {
            const date = moment(new Date());

            return date.locale("vi").format("dddd, Do MMMM");
        },

        time1() {
            const date = moment(new Date());

            return date.locale("vi").format("hh::mm ");
        },

        time2() {
            const date = moment(new Date());

            return date.locale("vi").format("dddd");
        },
    },

    methods: {
        async getAPI() {
            try {
                const data = await getData({
                    q: this.city,
                    appid: "c34d0b30de706ed953190741dcd852f2",
                    units: "metric",
                    lang: "vi",
                }).then((response) => ({
                    data: response.data,
                }));

                this.data = data;
            } catch (e) {
                return { data: [] };
            }
        },

        temperature(value) {
            if (value.data) {
                const temperature = Math.round(value.data.main.temp);
                return temperature;
            }

            return 23;
        },
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    display: flex;
    justify-content: center;
}
.container {
    width: 350px;
    height: 650px;
    background: rgb(254,219,101);
    background: linear-gradient(360deg, rgba(254,219,101,1) 0%, rgba(214,99,134,1) 100%);
    border-radius: 15px;
}
.time {
    color: white;
    margin: 40px;
    font-size: 20px;
}

.hour {
    color: white;
    margin: 40px;
    font-size: 25px;
    font-weight: bold;
}

.address {
    color: white;
    margin: 40px;
    font-size: 20px;
    text-transform: capitalize;
    margin-bottom: 5px;
}

.iamge {
    width: 200px;
}

.temperature {
    color: white;
    font-size: 80px;
    font-weight: lighter;
    line-height: 1;
    position: relative;
    margin-top: 10px;
    margin-bottom: 50px;
}

.temperature::after {
    content: "o";
    position: absolute;
    font-size: 30px;
}

.current-day {
    color: white;
    font-size: 27px;
}

.line {
    width: 80%;
    border: 1px solid white;
    margin-left: 10%;
    margin-bottom: 20px;
}

.footer {
    display: flex;
    justify-content: space-around;
    color: white;
}

.font-bold {
    font-weight: bold;
    margin: 5px;
}

.font-nomal {
    font-weight: normal;
}

.footer-left,
.footer-right {
    display: flex;
    flex-direction: column;
    align-items: baseline;
}

.search-bar {
    width: 50%;
    margin: 0 auto;
    padding-top: 20px;
    display: flex;
    position: relative;
    justify-content: center;
    align-items: center;
    border-bottom: 1px solid white;
}

.search-icon {
    margin-right: 5px;
    position: absolute;
    left: 0;
    color: white;
}

.search-input {
    border: 0;
    outline: 0;
    padding: 3px 3px 3px 20px;
    background: transparent;
    height: 20px;
    color: white;
    margin-left: 30px;
}

.search-input::placeholder {
    color: rgb(206, 206, 206);
    margin-left: 30px;
}
</style>
