<template>
  <div class="main">
    <h1>{{ content }}</h1>
    <div class="search">
        <div class="search_bar">
            <img src="../assets/search.svg">
            <input type="search" v-model="query" placeholder="Поиск по имени или e-mail">
        </div>
        <p><img src="../assets/clear.svg"> Очистить фильтр</p>
    </div>
    <div class="sort">
        Сортировка: <span :key="index" @click="SortByDate()">Дата регистрации</span> <span :key="index" @click="SortByRate()">Рейтинг</span>
    </div>
    <div class="list">
        <table id="user-list">
            <tr>
                <th>Имя пользователя</th>
                <th>E-mail</th>
                <th>Дата регистрации</th>
                <th>Рейтинг</th>
                <th/>
            </tr>
            <tr v-for="(user,index) in paginatedUsers" :key="user.id">
                <td><span class="username">{{ user.username }}</span></td>                    
                <td>{{ user.email }}</td>
                <td>{{ new Date(user.registration_date).toISOString().replace(/T/, ' ').replace(/\..+/, '') }}</td>
                <td>{{ user.rating }}</td>
                <td><img class="remove" src="../assets/remove.svg" @click="removeUser(index)"></td>
            </tr>
        </table>
        <div class="page" v-for="page in pages" :key="page" @click="SelectPage(page)" :class="{'page_selected': page === PageNumber}"> {{ page }} </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'HelloWorld',
    props: {
        content: String
    },
    data() {
      return {
            users: [],
            UsersPerPage: 10,
            PageNumber: 1,
            SortParam: null,
            query: ''
        }
    },
    mounted() {
        axios
            .get('https://5ebbb8e5f2cfeb001697d05c.mockapi.io/users')
            .then(response => (this.users = response['data']))
            .catch(function(error) {
                console.log(error);
            })
    },
    computed: {
        pages() {
            return Math.ceil(this.users.length / this.UsersPerPage);
        },
        paginatedUsers() {
            if(this.query.length > 0) {
                return this.users.filter(user => {
                    return user.username.toLowerCase().indexOf(this.query.toLowerCase()) !== -1 || user.email.toLowerCase().indexOf(this.query.toLowerCase()) !== -1;
                })
            }
            else {
                let from = (this.PageNumber - 1) * this.UsersPerPage;
                let to = from + this.UsersPerPage;
                return this.users.slice(from, to);
            } 
        }
    },
    methods: {
        SelectPage(page) {
            this.PageNumber = page;
        },
        removeUser(index) {
            this.users.splice(index,1)
        },
        SortByRate() {
            if(this.SortParam === 'Rate') {
                this.SortParam = 'Rate_reverse';
                this.users.reverse();
            }
            else {
                this.SortParam = 'Rate';
                this.users.sort((a,b) => a.rating - b.rating)
            }
        },
        SortByDate() {
            if(this.SortParam === 'Date') {
                this.SortParam = 'Date_reverse';
                this.users.reverse();
            }
            else {
                this.SortParam = 'Date';
                this.users.sort((a,b) => Date(a.registration_date).getTime() - Date(b.registration_date).getTime())
            }
        }
    }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
    font-family: Inter;
    font-style: normal;
    font-weight: 500;
    font-size: 12px;
    line-height: 16px;
    /* identical to box height, or 133% */


    /* Gray 2 */

    color: #4F4F4F;
}
h1 {
    position: relative;
    font-family: 'Inter', sans-serif;
    font-style: normal;
    font-weight: bold;
    font-size: 24px;
    line-height: 28px;
    /* identical to box height, or 117% */


    color: #333333;
    
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.search {
    display: block;
    background: white;
    box-shadow: 0px 18px 15px rgba(148, 148, 148, 0.15);
    border-radius: 7px;
    height: 120px;
    width: 90%;
    position: relative;
    left: 5%;
}
.search_bar {
    display: block;
    background: #ECEFF0;
    border-radius: 4px;
    width: 90%;
    position: absolute;
    left: 5%;
    top: 10%;
    min-height: 40px;
}
.search_bar img {
    position: absolute;
    left: 5px;
    top: 5%;
    width: 36px;
    height: 36px;
}
.search_bar input {
    border: none;
    background: #ECEFF0;
    position: absolute;
    left: 50px;
    height: 36px;
    font-family: 'Inter', sans-serif;
    font-style: normal;
    font-weight: 500;
    font-size: 20px;
    line-height: 12px;
    /* identical to box height, or 133% */


    /* names tablet */

    color: #9EAAB4;
}
.search_bar:active {
    border: none;
}
.search p {
    position: absolute;
    left: 5%;
    top: 50%;
    font-family: Inter;
    font-style: normal;
    font-weight: 500;
    font-size: 18px;
    /* identical to box height, or 133% */


    /* Gray 2 */

    color: #4F4F4F;
}
.search p img {
    width: 24px;
    height: 24px;
    transform: translateY(5px);
}
.list {
    display: block;
    position: relative;
    left: 5%;
    width: 90%;
    top: 100%;
    font-family: Inter;
    font-style: normal;
    font-weight: 500;
    font-size: 12px;
    line-height: 16px;
    /* identical to box height, or 133% */


    /* font */

    color: #ACACAC;
    background: #FFFFFF;
    border-radius: 7px;
}
.list span {
    font-family: Inter;
    font-style: normal;
    font-weight: bold;
    font-size: 12px;
    line-height: 16px;
    /* identical to box height, or 133% */


    /* username */

    color: #0CB4F1;
}
table {
    width: 90%;
    height: 50%;
}
td, th {
    text-align: center; /* Выравнивание по центру */
    padding: 3px; /* Поля вокруг текста */
    border-bottom: 1px solid #F7F7F7;
}
.sort {
    display: block;
    position: relative;
    text-align: left;
    width: 90%;
    left: 5%;
    padding: 50px 0 10px 0;
}
.page {
    padding: 10px;
    height: 10px;
    width: 10px;
    font-size: 20px;
    display: inline-block;
    color: white;
    background: #9EAAB4;
}
.page:hover {
    cursor: pointer;
    background: #0b87b4;
}
.page_selected {
    background: #0CB4F1;
}
.remove {
    padding: 2px;
}
.remove:hover {
    cursor: pointer;
}
.sort span:hover {
    cursor: pointer;
    color: #0CB4F1;
}
</style>
