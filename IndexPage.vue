<template>
    <h1>Filmes em destaque</h1>

  <div class="film-grid">
      <div v-for="(film, index) in films" :key="index" class="film-card">
        <h3>{{ film.title }}</h3>
        <h6>{{ film.buyPrice }}</h6>
        <h6>{{ film.rentPrice }}</h6>
        <p>{{ film.description }}</p>
        <div class="film-buttons">
          <button class="buy-button" @click="addToCart(film, 'Compra')">Comprar</button>
          <button class="rent-button" @click="addToCart(film, 'Aluguel')">Alugar</button>
        </div>
      </div>
  </div>

    <p v-if="messageCart" class="messageCart">{{ messageCart }}</p>
  <div class="cart-container">
      <h2 class="cartTitle">Seu Carrinho</h2>
      <table>
        <thead>
          <tr>
            <th>Filme</th>
            <th>Opção</th>
            <th>Preço</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in cartItems" :key="index">
            <td>{{ item.film.title }}</td>
            <td>{{ item.option }}</td>
            <td>{{ formatPrice(item.price) }}</td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="2" class="total-label">Total:</td>
            <td>{{ formatPrice(cartTotal) }}</td>
          </tr>
        </tfoot>
      </table>
      <div class="payment-button-container">
      <button class="payment-button" @click="openPaymentModal">Fechar Carrinho</button>
    </div>
    </div>

  <div v-if="showPaymentModal" class="modal-payment">
    <div class="modalPayment">
      <p class="titlePayment">Tela de Pagamento</p>
      <p>Filmes comprados ou alugados:</p>
      <ul>
          <li v-for="(item, index) in cartItems" :key="index">
          {{ item.film.title }} - {{ item.option }}: {{ formatPrice(item.price) }}
        </li>
      </ul>
      <p>Preço final: {{ formatPrice(cartTotal) }}</p>
      <p>Meios de pagamento:</p>
      <div class="paymentMethods">
        <span>Boleto</span>
        <span>Cartão</span>
        <span>Pix</span>
      </div>
      <button class="buttonClose" @click="showPaymentModal = false">Fechar</button>
    </div>
  </div>

  <div v-if="showLoginModal" class="modal-container">
      <div class="modal">
      <h2>Login</h2>
      <form @submit="login">
        <label for="username">Username:</label>
        <input type="email" id="username" v-model="loginForm.username">
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="loginForm.password">
        <button class="buttonLogin" type="submit">Login</button>
      </form>
      <p v-if="loginError" class="error-message">{{ loginError }}</p>
      <p>Ainda não possui uma conta? <button class="buttonRegister" v-on:click="openRegisterModal">Registre-se</button></p>
      </div>
    </div>
    
  <div v-if="showRegisterModal" class="modal-container">
      <div class="modal">
      <h2>Cadastro de usuário</h2>
      <form @submit="register">
        <label for="username">Username:</label>
        <input type="email" id="username" v-model="registerForm.username">
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="registerForm.password">
        <button type="submit">Registre-se</button>
      </form>
    </div>
    </div>

</template>

<script>
export default {
  data() {
    return {
      films: [
      {
        title: 'Suspense A',
        buyPrice: 'Comprar: R$20',
        rentPrice: 'Alugar: R$8',
        description: 'Descrição do Filme 1',
        priceBuy: 20.00,
        priceRent: 8.00,
      },
      {
        title: 'Infantil A',
        buyPrice: 'Comprar: R$22',
        rentPrice: 'Alugar: R$10',
        description: 'Descrição do Filme 2',
        priceBuy: 22.00,
        priceRent: 10.00,
      },
      {
        title: 'Drama A',
        buyPrice: 'Comprar: R$15',
        rentPrice: 'Alugar: R$6',
        description: 'Descrição do Filme 3',
        priceBuy: 15.00,
        priceRent: 6.00,
      },
      {
        title: 'Ficção A',
        buyPrice: 'Comprar: R$16',
        rentPrice: 'Alugar: R$4',
        description: 'Descrição do Filme 4',
        priceBuy: 16.00,
        priceRent: 4.00,
      },
      {
        title: 'Ficção B',
        buyPrice: 'Comprar: R$30',
        rentPrice: 'Alugar: R$18',
        description: 'Descrição do Filme 5',
        priceBuy: 30.00,
        priceRent: 18.00,
      },
      {
        title: 'Suspense B',
        buyPrice: 'Comprar: R$40',
        rentPrice: 'Alugar: R$27',
        description: 'Descrição do Filme 6',
        priceBuy: 40.00,
        priceRent: 27.00,
      },
      {
        title: 'Infantil B',
        buyPrice: 'Comprar: R$10',
        rentPrice: 'Alugar: R$4',
        description: 'Descrição do Filme 7',
        priceBuy: 10.00,
        priceRent: 4.00,
      },
      {
        title: 'Drama B',
        buyPrice: 'Comprar: R$14',
        rentPrice: 'Alugar: R$6',
        description: 'Descrição do Filme 8',
        priceBuy: 14.00,
        priceRent: 6.00,
      },
      {
        title: 'Infantil C',
        buyPrice: 'Comprar: R$18',
        rentPrice: 'Alugar: R$10',
        description: 'Descrição do Filme 9',
        priceBuy: 18.00,
        priceRent: 10.00,
      },
      {
        title: 'Drama C',
        buyPrice: 'Comprar: R$13',
        rentPrice: 'Alugar: R$5',
        description: 'Descrição do Filme 10',
        priceBuy: 13.00,
        priceRent: 5.00,
      },
      ],

      showLoginModal: false,
      showRegisterModal: false,
      showPaymentModal: false,
      loginForm: {
        username: '',
        password: '',
      },
      registerForm: {
        username: '',
        password: '',
      },
      isLoggedIn: false,
      cartItems: [],
      loginError: '',
      messageCart: '',
    };
  },
  methods: {
    checkUserLoggedIn() {
      const userLoggedIn = localStorage.getItem('isLoggedIn');
      this.isLoggedIn = userLoggedIn === 'true'; // Converte para booleano
      return this.isLoggedIn;
    },
    register(event) {
      event.preventDefault();
      // Lógica de registro
      const { username, password } = this.registerForm;
      // Armazenar os dados de registro no localStorage
      localStorage.setItem('username', username);
      localStorage.setItem('password', password);
      this.showRegisterModal = false;
    },
    login(event) {
      event.preventDefault();
      // Lógica de login
      const { username, password } = this.loginForm;
      // Verificar se o username e password correspondem a um usuário registrado

      // Exemplo de verificação simples
      if (username === localStorage.getItem('username') && password === localStorage.getItem('password')) {
        // Usuário logado com sucesso
        localStorage.setItem('isLoggedIn', 'true');
        this.isLoggedIn = true;
        this.showLoginModal = false;
        this.loginError = '';
      } else if (username != localStorage.getItem('username')) { 
        this.loginError = "Usuário não cadastrado";
      }
      else {
        // Credenciais inválidas, exibir mensagem de erro
        this.loginError = 'Usuário ou senha incorretos';
      }
    },

    redirectToLoginOrBuy(film) {
      const userLoggedIn = localStorage.getItem('isLoggedIn');
      if (userLoggedIn) {
        addToCart(film, 'Compra');
      } else {
        this.showLoginModal = true;
      }
    },

    redirectToLoginOrRent(film) {
      const userLoggedIn = localStorage.getItem('isLoggedIn');
      if (userLoggedIn) {
        addToCart(film, 'Aluga');
      } else {
        this.showLoginModal = true;
      }
    },

    created() {
      this.checkUserLoggedIn();
    },

    openRegisterModal() {
      this.showLoginModal = false;
      this.showRegisterModal = true;
    },

    addToCart(film, option) {
      const userLoggedIn = this.checkUserLoggedIn();
      if (userLoggedIn) {
        // Verificar se o filme já está no carrinho
        const filmInCart = this.cartItems.find(item => item.film.title === film.title && item.option === option);
        if (filmInCart) {
          this.messageCart = "O filme já está no carrinho";
          return;
        }
        else { 
          this.messageCart = "";
        }

        const price = option === '' ? film.priceBuy : film.priceRent;
        const cartItem = {
          film,
          option,
          price,
        };
        this.cartItems.push(cartItem);
      } else {
        this.showLoginModal = true;
      }
    },
    openPaymentModal() {
    this.showPaymentModal = true;
    },
    formatPrice(price) {
      return price.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
    },
  },
  computed: {
    cartTotal() {
      return this.cartItems.reduce((total, item) => total + item.price, 0);
    },
  },
};
</script>

<style scoped>
.film-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-gap: 20px;
  margin: 0 auto;
  max-width: 1000px;
}

.film-card {
  border: 1px solid #ccc;
  position: relative;
  left: -90px;
  padding: 20px;
  background: rgb(2, 0, 36);
  background: linear-gradient(
    145deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(9, 9, 121, 1) 32%,
    rgba(0, 212, 255, 1) 100%
  );
  color: antiquewhite;
}

.film-buttons {
  margin-top: 10px;
  display: flex;
  justify-content: space-between;
}

.buy-button { 
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  cursor: pointer;
  transition-duration: 0.4s;
  margin-right: 10px;
}
.rent-button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  cursor: pointer;
  transition-duration: 0.4s;
  margin-left: 10px;
}
.buy-button:hover,
.rent-button:hover {
  background-color: #45a049;
}

h1 {
  text-align: center;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
}
.modal-container {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.modal {
  background-color: #fff;
  border-radius: 5px;
  padding: 20px;
  max-width: 400px;
}

.modal h2 {
  text-align: center;
  margin-bottom: 20px;
  position: relative;
  bottom: 40px;
}
p { 
  text-align: center;
}
.modal form {
  display: flex;
  flex-direction: column;
  position: relative;
  bottom: 30px;
}

.modal label {
  font-weight: bold;
  margin-bottom: 5px;
}

.modal input[type="email"],
.modal input[type="password"] {
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.modal button {
  background-color: #4caf50;
  color: #fff;
  border: none;
  position: relative;
  top: 5px;
  padding: 10px;
  text-align: center;
  text-decoration: none;
  font-size: 14px;
  cursor: pointer;
}

.modal button:hover {
  background-color: #45a049;
}
.error-message {
  color: red;
  margin-top: 10px;
}
.messageCart { 
  color: red;
  margin-top: 10px;
  font-weight: bold;
  font-size: 20pt;
}
.payment-button { 
  padding: 8px 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  position: relative;
  top: 20px;
  left: 250px;
}
button {
  padding: 8px 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
.cartTitle { 
  text-align: center;
}
.cart-container {
  max-width: 800px;
  margin: 0 auto;
  margin-top: 50px;
  margin-bottom: 40px;
}
.cart-container table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
.cart-container table th,
.cart-container table td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}
.cart-container table th {
  font-weight: bold;
}
.cart-container table tfoot td {
  font-weight: bold;
}
.total-label {
  text-align: right;
}
.modal-payment {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.modalPayment {
  background-color: #fff;
  border-radius: 5px;
  padding: 20px;
  max-width: 400px;
  height: 420px;
}
.titlePayment { 
  font-size: 20pt;
  font-weight: bold;
}
.paymentMethods {
  display: flex;
  align-items: center;
  margin-top: 10px;
  position: relative;
  left: 40px;
}
.paymentMethods span {
  margin-right: 10px;
  border-right: 1px solid #000;
  padding-right: 10px;
}
.paymentMethods span:last-child {
  border-right: none;
  padding-right: 0;
}
.buttonClose { 
  padding: 8px 16px;
  background-color: #4caf50;
  color: white;
  border: none;
  cursor: pointer;
  position: relative;
  top: 25px;
  left: 80px;
}
</style>