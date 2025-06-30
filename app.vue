<template>
  <div>
    <button @click="redirectToAuth">Login</button>
    <NuxtPage />
  </div>
</template>

<script>
export default {
  setup() {
    const API_KEY = '8fe8ba4797cf9dd9f53aad6b4fe11134'
    const url = `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}&language=en-US&page=1`
    const REDIRECT_URL = 'https://tmdb-emnuolha7-mihanews-projects.vercel.app/auth-callback';

    async function getRequestToken() {
      const response = await fetch(
        `https://api.themoviedb.org/3/authentication/token/new?api_key=${API_KEY}`
      );
      const data = await response.json();
      console.log('request_token', data.request_token)
      return data.request_token;
    }

    async function redirectToAuth() {
      const token = await getRequestToken();
      localStorage.setItem('temp_request_token', token);
      
      window.location.href = `https://www.themoviedb.org/authenticate/${token}?redirect_to=${encodeURIComponent(REDIRECT_URL)}`;
    }

    // async function handleAuthCallback() {
    //   const urlParams = new URLSearchParams(window.location.search);
    //   const approved = urlParams.get('approved');
    //   const requestToken = localStorage.getItem('temp_request_token');

    //   if (approved === 'true' && requestToken) {
    //     const sessionId = await createSession(requestToken);
    //     localStorage.setItem('tmdb_session_id', sessionId);
        
    //     window.location.href = '/profile';
    //   }
    // }

    // async function createSession(requestToken) {
    //   const response = await fetch(
    //     `https://api.themoviedb.org/3/authentication/session/new?api_key=${API_KEY}`,
    //     {
    //       method: 'POST',
    //       headers: {'Content-Type': 'application/json'},
    //       body: JSON.stringify({request_token: requestToken})
    //     }
    //   );
    //   const data = await response.json();
    //   console.log('session_id', data.session_id)
    //   return data.session_id;
    // }



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
