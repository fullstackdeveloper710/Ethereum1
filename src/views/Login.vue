<template>
  <div class="login_section">
    <form @submit.prevent="login" class="login_form">
      <h1 class="head_section">Ethereum</h1>
      <label for="email">Email:</label>
      <input type="text" id="email" v-model="email" required />

      <label for="password">Password:</label>
      <input
        type="password"
        id="password"
        v-model="password"
        @input="checkPasswordStrength"
      />

      <div class="password-strength">
        <div
          v-for="(indicator, index) in passwordIndicators"
          :key="index"
          :style="{ backgroundColor: getPasswordStrengthColor(index) }"
        >
          {{ indicator.label }}
        </div>
        <span v-if="passwordStrength > 0" class="pass_strength">{{
          mapStrengthToText()
        }}</span>
      </div>
      <span class="error_msg"> {{ passwordError }}</span>
      <button type="submit">Login</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      email: "",
      loggedIn: false,
      password: "",
      passwordStrength: 0,
      passwordIndicators: [
        { label: "", regex: /.{8,}/ },
        { label: "", regex: /[A-Z]/ },
        { label: "", regex: /[a-z]/ },
        { label: "", regex: /\d/ },
        {
          label: "",
          regex: /[^A-Za-z0-9]/,
        },
      ],
      strengthColors: ["#FF0000", "#FF6347", "#FFD700", "#ADFF2F"],
      passwordError: null,
    };
  },
  methods: {
    mapStrengthToText() {
      switch (this.passwordStrength) {
        case 1:
          return "Too Short";
        case 2:
          return "Weak";
        case 3:
          return "Good";
        case 4:
          return "Fair";
        case 5:
          return "Strong";
        default:
          return;
      }
    },
    navigateToDashboard() {
      this.$router.push("/dashboard");
    },
    async login() {
      if (this.email && this.password) {
        if (this.passwordStrength > 2) {
          try {
            // Make a GET request to the API
            const response = await axios.post(
              "https://api.forgingblock.io/signin",
              {
                email: this.email,
                password: this.password,
              },
              {
                headers: {
                  "Content-Type": "application/x-www-form-urlencoded",
                },
              }
            );
            if (response.data) {
              this.loggedIn = true;
              localStorage.setItem("token", response.data.user);
              this.navigateToDashboard();
            }
          } catch (error) {
            console.error("Error fetching data:", error);
          }
        } else {
          this.passwordError = "Please enter a valid password ";
        }
      } else {
        alert("Please enter both email and password.");
      }
    },
    checkPasswordStrength() {
      this.passwordStrength = this.passwordIndicators.reduce(
        (strength, indicator) =>
          this.password.match(indicator.regex) ? strength + 1 : strength,
        0
      );
    },
    getPasswordStrengthColor(index) {
      return this.passwordStrength > index
        ? this.strengthColors[index]
        : "#ecf0f1";
    },
  },
  beforeMount() {
    const token = localStorage.getItem("token");
    if (token) {
      this.$router.push("/dashboard");
    }
  },
};
</script>

<style scoped>
.login_section {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  flex-direction: column;
}

.login_form {
  display: flex;
  flex-direction: column;
  text-align: left;
  margin-bottom: 20px;
  width: 30%;
}

label {
  display: block;
  margin-bottom: 5px;
}

input {
  margin-bottom: 10px;
  padding: 10px 5px;
  width: 100%;
  border: 1px solid gray;
}

.error_msg {
  margin: 5px 0px;
  color: red;
}

button {
  padding: 10px 5px;
  background-color: #3498db;
  color: #fff;
  border: none;
  cursor: pointer;
  margin-top: 20px;
}

.password-strength {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 10px;
}
.password-strength div {
  flex: 1;
  background-color: #ecf0f1;
  padding: 2px;
  margin-right: 5px;
  text-align: center;
  border-radius: 5px;
  height: 5px;
}

.password-strength span{
  font-size: 10px;
  margin-bottom: 0px;
}

.password-strength div.active {
  background-color: #3498db;
  color: #fff;
}
.head_section {
  text-align: center;
}
</style>
