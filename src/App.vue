<template>
  <main class="min-h-screen">
    <article>
      <section>
        <div>
          <app-navbar />

          <router-view />
        </div>
      </section>
    </article>
  </main>
</template>

<script>

import AppNavbar from '@/components/app/AppNavbar'

export default {
  name: 'App',

  components: {
    AppNavbar
  }
}
</script>
