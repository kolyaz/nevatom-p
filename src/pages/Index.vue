<template>
   <q-page class="flex flex-center">
    <div class="q-pa-md" style="max-width: 800px">
      <transition
      transition-show="jump-down"
      >
        <q-form
        class="q-gutter-md"
        transition-show="jump-down"
      >

        <q-input v-model="login"
          filled
          label="Имя пользователя "
          hint="введите свой логин"
          lazy-rules
          :rules="[ val => val && val.length > 0 || 'введите  логин']"
        >
          <template v-slot:append>
            <q-icon name="how_to_reg" />
          </template>
        </q-input>

        <q-input
          v-model="password"
          label="Пароль "
          lazy-rules
          :rules="[ val => val && val.length > 0 || 'введите корректный пароль']"
          filled :type="isPwd ? 'password' : 'text'" hint="введите  пароль"
        >
          <template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            />
            <!-- <q-icon name="https" /> -->
          </template>
        </q-input>

        <q-input
          v-model="ip"
          @keyup.enter="setups"
          filled
          label="Адрес устройства"
          hint="введите свой адрес"
          lazy-rules
          :rules="[
          val => val && val.length > 0 || 'Введите адрес',
          ]"

        >
          <template v-slot:append>
            <q-icon name="vpn_lock" />
          </template>
        </q-input>

        <q-toggle v-model="accept" label="Сохранить данные для входа" />

        <div>
          <q-btn @click="setups"  label="Войти" type="submit" color="green" />
        </div>
      </q-form>
      </transition>
    </div>
  </q-page>
</template>

<script>
import { openURL, LocalStorage, Notify } from 'quasar';
import { ref } from 'vue';

// export default defineComponent({
//   name: 'PageIndex'
// })

export default {
  setup() {
    return {
      pass: ref(''),
      isPwd: ref(true),
    };
  },
  data() {
    if (LocalStorage.getItem('storage')) {
      const {
        login, password, ip, accept,
      } = LocalStorage.getItem('storage');
      return {
        login,
        password,
        ip,
        accept,
      };
    }
    return {
      login: 'root',
      password: 'segnetics',
      ip: '',
      accept: false,

    };
  },
  methods: {
    setups() {
      if (this.accept) {
        LocalStorage.set('storage',
          {
            login: this.login,
            password: this.password,
            ip: this.ip,
            accept: this.accept,
          });
      } else {
        LocalStorage.remove('storage');
      }
      if (this.ip && this.login && this.login && this.password) {
        Notify.create({
          spinner: true,
          message: 'Ожидайте...',
          timeout: 2000,
          color: 'positive',
          position: 'center',
        });
        openURL(
          `http://${this.ip}/login.php?login=${this.login}&password=${this.password}`,
          null,
          {
            noopener: false, // this is set by default for security purposes
            menubar: true,
            toolbar: true,
            noreferrer: true,
          },
        );
      } else {
        Notify.create({
          message: 'Заполните все поля формы',
          color: 'negative',
          icon: 'report_problem',
          position: 'top',
        });
      }
    },
  },

};
</script>
