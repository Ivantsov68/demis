<template>
  <div class="jumbotron">
    <div class="container">
      <div class="row">
        <div class="col-sm-8 offset-sm-2">
          <div>
            <h1>Оформить заявку</h1>
            <form @submit.prevent="handleSubmit">
              <div
                class="form-group"
                :class="{ 'form-group--error': $v.user.name.$error }"
              >
                <label for="Name">ФИО</label>
                <input
                  type="text"
                  v-model="user.name"
                  id="Name"
                  name="Name"
                  class="form-control"
                  placeholder="Иванов Иван Иванович"
                />
                <span class="error" v-if="!$v.user.name.required"
                  >ФИО обязательны для ввода</span
                >
                <span class="error" v-if="!$v.user.name.minLength"
                  >ФИО должны содержать не менее 15 символов</span
                >
              </div>

              <div
                class="form-group"
                :class="{ 'form-group--error': $v.user.email.$error }"
              >
                <label for="email">Email</label>
                <input
                  type="email"
                  v-model="user.email"
                  id="email"
                  name="email"
                  class="form-control"
                  placeholder="ivanscov@rambler.ru"
                />
                <span class="error" v-if="!$v.user.email.required"
                  >Email обязателен для ввода</span
                >
                <span class="error" v-if="!$v.user.email.email"
                  >Некорректный Email</span
                >
              </div>

              <div
                class="form-group"
                :class="{ 'form-group--error': $v.user.address.$error }"
              >
                <label for="address">Адрес</label>
                <input
                  type="text"
                  v-model="user.address"
                  id="address"
                  name="address"
                  class="form-control"
                  placeholder="г.Тамбов, ул.Советская, д.1"
                />
                <span class="error" v-if="!$v.user.address.required"
                  >Адрес обязателен для ввода</span
                >
                <span class="error" v-if="!$v.user.address.maxLength"
                  >Адрес должен содержать не более 30 символов</span
                >
              </div>

              <div
                class="form-group"
                :class="{ 'form-group--error': $v.user.number.$error }"
              >
                <label for="number">Телефон</label>
                <input
                  type="text"
                  v-model="user.number"
                  id="number"
                  name="number"
                  class="form-control"
                  placeholder="+7"
                />
                <span class="error" v-if="!$v.user.number.minLength"
                  >Номер телефона содержит 11 цифр</span
                >
                <span class="error" v-if="!$v.user.number.maxLength"
                  >Номер телефона содержит 11 цифр</span
                >
                <span class="error" v-if="!$v.user.number.required"
                  >Номер телефона обязателен для ввода</span
                >
              </div>

              <div class="form-btn">
                <button class="btn btn-primary" :disabled="$v.$invalid">
                  Отправить заявку
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
    <div class="card__content">
      <div class="container">
        <div id="league-table" class="table-responsive">
          <table class="table table--lg team-roster-table table-hover">
            <thead>
              <th class="team-roster-table__name">ФИО</th>
              <th class="team-roster-table__name">Email</th>
              <th class="team-roster-table__name">Адрес</th>
              <th class="team-roster-table__name">Телефон</th>
            </thead>

            <tbody>
              <tr v-for="(league, index) in userForTable" :key="index">
                <td class="team-roster-table__name">{{ league.name }}</td>
                <td class="team-roster-table__name">{{ league.email }}</td>
                <td class="team-roster-table__name">{{ league.address }}</td>
                <td class="team-roster-table__name">{{ league.number }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <div class="news">
      <div class="container">
        <h2 class="title-news">Новости</h2>
        <div class="news-block">
          <div
            v-for="(el, index) of news.slice(first, second)"
            :key="index"
            class="news"
          >
            <div class="card-news">
              <div class="wrapper-img">
                <img
                  :src="el.urlToImage"
                  alt=""
                  class="news-img"
                  @click="zoom"
                />
              </div>

              <div id="myModal" class="modal">
                <span class="close">&times;</span>

                <img class="modal-content" id="img01" />

                <div id="caption">
                  <h4 class="title">{{ el.title }}</h4>
                  <p>{{el.description}}</p>
                  <p class="author">{{ el.author }}</p>
                </div>
              </div>
              <h4 class="title">{{ el.title }}</h4>
              <p class="publish">{{ el.publishedAt }}</p>
              <p class="author">{{ el.author }}</p>
            </div>
          </div>
        </div>
        <div class="form-btn">
          <button class="btn btn-primary" @click="moreNews">
            Больше новостей
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import {
  required,
  minLength,
  email,
  maxLength,
} from "vuelidate/lib/validators";
export default {
  name: "app",
  data() {
    return {
      user: {
        name: "",
        email: "",
        address: "",
        number: "",
      },
      userForTable: [],
      news: [],
      first: 0,
      second: null,
    };
  },
  created() {
    this.loadNextImage();
    this.second = 3;
  },
  methods: {
    handleSubmit() {
      this.userForTable.push({
        name: this.user.name,
        email: this.user.email,
        address: this.user.address,
        number: this.user.number,
      });
      this.user.name = "";
      this.user.email = "";
      this.user.address = "";
      this.user.number = "";
    },
    async loadNextImage() {
      try {
        axios.defaults.headers.common["x-api-key"] =
          "f172068fe2554025b744f63989b919fc";

        let response = await axios.get(
          "https://newsapi.org/v2/everything?q=bitcoin"
        );

        this.news.push(...response.data.articles);
      } catch (err) {
        console.log(err);
      }
    },
    moreNews() {
      this.second += 3;
    },
    zoom(e) {
      let modal = document.getElementById("myModal");

      let modalImg = document.getElementById("img01");

      modal.style.display = "block";
      modalImg.src = e.target.src;

      let span = document.getElementsByClassName("close")[0];

      span.onclick = function () {
        modal.style.display = "none";
      };
    },
  },
  validations: {
    user: {
      name: {
        required,
        minLength: minLength(15),
      },
      email: {
        required,
        email,
      },
      address: {
        required,
        maxLength: maxLength(30),
      },
      number: {
        required,
        minLength: minLength(12),
        maxLength: maxLength(12),
      },
    },
  },
};
</script>

<style scoped>
.table td,
.table th {
  text-align: center;
}

.table {
  margin: 0 auto;
}

span {
  padding-top: 0px;
  margin-top: 0px;
  font-size: 12px;
  color: red;
}

input {
  font-size: 30px;
  border: 1px double rgb(102, 97, 96);
  border-radius: 4px;
}

h1 {
  text-align: center;
}

img {
  width: 350px;
  height: 350px;
  object-fit: cover;
}

.jumbotron {
  margin-bottom: 0;
}

.card__content {
  margin-top: 50px;
}

.news-block {
  display: flex;
  flex-wrap: wrap;
  column-gap: 20px;
  justify-content: center;
}

.title-news {
  margin: 50px 0;
  text-align: center;
}

.card-news {
  max-width: 350px;
  text-align: center;
}

.form-btn {
  text-align: center;
  padding-top: 20px;
}

.btn {
  background-color: #be1116;
  color: #fff;
  border: 2px solid #be1116;
  text-transform: uppercase;
}

.btn:hover {
  color: #be1116;
  background: #fff;
}

.modal {
  display: none; 
  position: fixed; 
  z-index: 1; 
  padding-top: 100px; 
  left: 0;
  top: 0;
  width: 100%; 
  height: 100%; 
  overflow: auto; 
  background-color: rgb(0, 0, 0); 
  background-color: rgba(0, 0, 0, 0.9); 
}

.modal-content {
  margin: auto;
  display: block;
  width: 80%;
  max-width: 700px;
}

#caption {
  margin: auto;
  display: block;
  width: 80%;
  max-width: 700px;
  text-align: center;
  color: #ccc;
  padding: 10px 0;
  height: 150px;
}

.modal-content,
#caption {
  animation-name: zoom;
  animation-duration: 0.6s;
}

@keyframes zoom {
  from {
    transform: scale(0);
  }
  to {
    transform: scale(1);
  }
}

.close {
  position: absolute;
  top: 15px;
  right: 35px;
  color: #f1f1f1;
  font-size: 40px;
  font-weight: bold;
  transition: 0.3s;
}

.close:hover,
.close:focus {
  color: #bbb;
  text-decoration: none;
  cursor: pointer;
}
</style>


