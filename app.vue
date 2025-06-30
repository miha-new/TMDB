<template>
  <div>
    <form @submit.prevent="handlerLogin">
      <input type="text" placeholder="Name" required>
      <input type="password" placeholder="Password" required>
      <button type="submit">Войти</button>
    </form>
    <NuxtPage />
  </div>
</template>

<script>
export default {
  setup() {
    const API_KEY = '8fe8ba4797cf9dd9f53aad6b4fe11134'
    const url = `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}&language=en-US&page=1`

    async function getRequestToken() {
      const response = await fetch(
        `https://api.themoviedb.org/3/authentication/token/new?api_key=${API_KEY}`
      )
      const data = await response.json();
      console.log('request_token', data.request_token)
      return data.request_token;
    }

    async function handlerLogin(event, value) {
      console.log(event, value)
      const requestToken = await getRequestToken();

      // Проверка учетных данных
      const isValid = await validateCredentials(requestToken, username, password);
      
      if (isValid) {
        const sessionId = await createSession(requestToken);
        localStorage.setItem('tmdb_session', sessionId);
        alert('success');
        loadUserData();
      } else {
        alert('error');
      }
    };

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



    fetch(url)
      .then(res => res.json())
      .then(data => {
        console.log(data)
      })
      .catch(error => {
        console.error('Error', error)
      })

    return {
      redirectToAuth
    }
  }
}
</script>
