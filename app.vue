<template>
  <div id="home">
    <div>Hello world from your Vue project. Below is Builder Content:</div>

    <div v-if="content || isPreviewing()">
      <div>
        page title:
        {{ content?.data?.title || 'Unpublished' }}
      </div>
      <Content
        model="page"
        :content="content"
        :api-key="BUILDER_PUBLIC_API_KEY"
        :enrich="true"  
        :customComponents="REGISTERED_COMPONENTS"
        
      />
    </div>
    <div v-else>Content not Found</div>
  </div>
</template>

<script setup>
import { Content, fetchOneEntry, isPreviewing } from '@builder.io/sdk-vue';

import HelloWorldComponent from './components/HelloWorld.vue';
import PartnersCard from './components/PartnersCard.vue';

// Register your Builder components
const REGISTERED_COMPONENTS = [
  {
    component: HelloWorldComponent,
    name: 'Hello World',
    canHaveChildren: true,
    inputs: [
      {
        name: 'text',
        type: 'string',
        defaultValue: 'World',
      },
    ],
  },
  {
    component: PartnersCard,
    name: 'PartnerCard',
    inputs: [
      {
        name: "partners",
        type: "list",
        subFields: [
          {
            name: "partner",
            type: "reference",
            model: "push-partner",
          },
        ],
      },
    ],
  },
];

// TODO: enter your public API key
const BUILDER_PUBLIC_API_KEY = '717f6f07a937428fb26823965e8bc41c'; // ggignore

const route = useRoute();

// fetch builder content data
const { data: content } = await useAsyncData(`builderData-page-${route.path}`, () =>
  fetchOneEntry({
    model: 'page',
    apiKey: BUILDER_PUBLIC_API_KEY,
    userAttributes: {
      urlPath: route.path,
    },
    enrich: true,
  })
);
</script>
