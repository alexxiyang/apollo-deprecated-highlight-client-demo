<template>
  <div>
    Please make sure start https://github.com/alexxiyang/apollo-deprecated-highlight-demo and listen on http://localhost:4000/
    <br/>
    {{ msg }}
  </div>
</template>

<script setup>
import { onMounted } from 'vue'
import { ApolloClient, InMemoryCache, HttpLink, from, ApolloLink, gql } from "@apollo/client";
import { onError } from "@apollo/client/link/error";
import { ApolloDeprecatedHighlightClient } from 'apollo-deprecated-highlight-client';
import { ref } from 'vue'

const msg = ref('Loading');

onMounted(() => {
  const httpLink = new HttpLink({
    uri: "http://localhost:4000/graphql"
  });

  const deprecatedLink = new ApolloLink((operation, forward) => {
    return forward(operation).map(ApolloDeprecatedHighlightClient(operation));
  });

  const client = new ApolloClient({
    link: from([deprecatedLink, httpLink]),
    cache: new InMemoryCache()
  });

  const GET_BOOKS_CARS = gql`
  query GetBooksCars {
    books {
      title
      author
    }
    cars {
      model
      make
    }
  }
`;

  client.query({
      query: GET_BOOKS_CARS,
  }).then((result) => {
    msg.value = 'Loaded deprecations! Please open the console to check deprecations.';
  })
})
</script>
