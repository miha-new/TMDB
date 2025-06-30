<template>
  <div>
    <form @submit.prevent="handlerLogin">
      <input v-model="formLoginName" type="text" placeholder="Name" required>
      <input v-model="formLoginPassword" type="password" placeholder="Password" required>
      <button type="submit">Войти</button>
    </form>
    <NuxtPage />
  </div>
</template>

<script setup>
import {ref} from 'vue'

const API_KEY = '8fe8ba4797cf9dd9f53aad6b4fe11134'
const url = `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}&language=en-US&page=1`

const formLoginName = ref('')
const formLoginPassword = ref('')

async function getRequestToken() {
  const response = await fetch(
    `https://api.themoviedb.org/3/authentication/token/new?api_key=${API_KEY}`
  )
  const data = await response.json();
  console.log('request_token', data.request_token)
  return data.request_token;
}

async function handlerLogin() {
  const requestToken = await getRequestToken();
  console.log(formLoginName.value, formLoginPassword.value)
  const isValid = await validateCredentials(requestToken, formLoginName.value, formLoginPassword.value);
  
  if (isValid) {
    const sessionId = await createSession(requestToken);
    localStorage.setItem('tmdb_session', sessionId);
    alert('success');
    loadUserData();
  } else {
    alert('error');
  }
}

async function createSession(requestToken) {
  const response = await fetch(
    `https://api.themoviedb.org/3/authentication/session/new?api_key=${API_KEY}`,
    {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({request_token: requestToken})
    }
  );
  const data = await response.json();
  console.log('session_id', data.session_id)
  return data.session_id;
}

async function loadUserData() {
  const sessionId = localStorage.getItem('tmdb_session');
  
  const accountRes = await fetch(
    `https://api.themoviedb.org/3/account?api_key=${API_KEY}&session_id=${sessionId}`
  );
  const account = await accountRes.json();
  
  const favoritesRes = await fetch(
    `https://api.themoviedb.org/3/account/${account.id}/favorite/movies?api_key=${API_KEY}&session_id=${sessionId}`
  );
  
  console.log(`user's data`, {
    account,
    favorites: await favoritesRes.json()
  });
}

async function validateCredentials(token, username, password) {
  const response = await fetch(
    `https://api.themoviedb.org/3/authentication/token/validate_with_login?api_key=${API_KEY}`,
    {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({
        username: username,
        password: password,
        request_token: token
      })
    }
  );
  
  return response.ok;
}

async function logout() {
  const sessionId = localStorage.getItem('tmdb_session');
  
  await fetch(
    `https://api.themoviedb.org/3/authentication/session?api_key=${API_KEY}`,
    {
      method: 'DELETE',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({session_id: sessionId})
    }
  );
  
  localStorage.removeItem('tmdb_session');
  location.reload();
}



fetch(url)
  .then(res => res.json())
  .then(data => {
    console.log(data)
  })
  .catch(error => {
    console.error('Error', error)
  })
</script>
