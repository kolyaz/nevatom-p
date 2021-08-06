<template>
   <q-item class="flex flex-center">
    <div class="q-pa-md" style="max-width: 800px">
      <q-form
        class="q-gutter-md"
      >
        <q-input
          v-model="login"
          filled
          label="Имя пользователя "
          hint="введите свой логин"
          lazy-rules
          :rules="[ val => val && val.length > 0 || 'введите корректный логин']"
        />
        <q-input
          type="password"
          v-model="password"
          filled
          label="Пароль "
          hint="введите свой пароль"
          lazy-rules
          :rules="[ val => val && val.length > 0 || 'введите корректный пароль']"
        />

        <q-input
          v-model="ip"
          @keyup.enter="setups"
          filled
          label="IP адрес устройства"
          hint="введите свой IP"
          lazy-rules
          :rules="[val => val && val.length > 0 || 'Введите корректный IP']"
        />

        <q-toggle v-model="accept" label="Сохранить данные для входа" />

        <div>
          <q-btn @click="setups"  label="Войти" type="submit" color="green" />
        </div>
      </q-form>

    </div>
  </q-item>
</template>

<script>
import { openURL, LocalStorage, Notify } from 'quasar';

// export default defineComponent({
//   name: 'PageIndex'
// })

export default {
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
