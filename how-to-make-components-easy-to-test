# How to write Vue components that are easy to test?

Video: https://www.youtube.com/watch?v=ubdZT8Rsk6o

```vue
<!-- components/PageProductList.vue -->
<template>
  <LayoutDefault>
    <ProductList />
  </LayoutDefault>
</template>
```

```vue
<!-- components/ProductList.vue -->
<template>
  <table>
    <thead>
      <tr>
        <th>
          <button
            aria-label="sort by id"
            @click="sortBy('id')"
          >
            ID
          </button>
        </th>
        <th>
          <button
            aria-label="sort by name"
            @click="sortBy('name')"
          >
            Name
          </button>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="product in productsSorted">
        <!-- ... -->
      </tr>
    </tbody>
  </table>
</template>

<script>
import { computed, defineComponent, ref } from 'vue';
import { useStore } from 'vuex';

export default defineComponent({
  name: 'ProductList',
  setup() {
    let store = useStore();
    let sortProperty = ref('id');
    let productsSorted = computed(() => [...store.state.products].sort(/* sorting magic */));

    return {
      productsSorted,
      sortBy(property) {
        sortProperty.value = property;
      },
    }
  },
});
</script>
```

```js
// components/ProductList.spec.js
import { createStore } from 'vuex';
import { mount } from '@vue/test-utils';

import ProductList from './ProductList.vue';

let store = createStore({
  state() {
    return { products: [{ id: 1, name: 'Product 1' }] };
  },
});

test('it should sort products according to selected sort property', () => {
  let wrapper = mount(ProductList, {
    global: {
      plugins: [store],
    },
  });

  // Test code ...
});

test('it should ...', () => {
  let differentStore = createStore({
    state() {
      return { products: [{ id: 666, name: 'Product X' }] };
    },
  });
  let wrapper = mount(ProductList, {
    global: {
      plugins: [differentStore],
    },
  });

  // Test code ...
});
```

```vue
<!-- components/ProductList.vue -->

<!-- ... -->

<script>
import { computed, defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'ProductList',
  props: {
    products: {
      default: () => [],
      type: Array,
    },
  },
  setup(props) {
    let sortProperty = ref('id');
    let productsSorted = computed(() => [...props.products].sort(/* sorting magic */));

    return {
      productsSorted,
      sortBy(property) {
        sortProperty.value = property;
      },
    }
  },
});
</script>
```

```js
// components/ProductList.spec.js
import { mount } from '@vue/test-utils';

import ProductList from './ProductList.vue';

test('it should sort products according to selected sort property', () => {
  let wrapper = mount(ProductList, {
    props: {
      products: [{ id: 1, name: 'Product 1' }],
    },
  });

  // Test code ...
});

test('it should ...', () => {
  let wrapper = mount(ProductList, {
    props: {
      products: [{ id: 666, name: 'Product X' }],
    },
  });

  // Test code ...
});
```

```vue
<!-- components/PageNewsArticle.vue -->
<template>
  <LayoutDefault>
    <NewsArticle />
  </LayoutDefault>
</template>

<script>
import { defineComponent } from 'vue';

import NewsArticle from './NewsArticle.vue';

export default defineComponent({
  name: 'PageNewsArticle',
  components: {
    NewsArticle,
  },
});
</script>
```

```vue
<!-- components/NewsArticle.vue -->
<template>
  <div v-if="article">
    <div v-html="article" />
    <button @click="like">
      Like this article
    </button>
    <NewsComments />
  </div>
  <div v-else>
    Loading ...
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

import NewsComments from './NewsComments.vue';

export default defineComponent({
  name: 'NewsArticle',
  components: {
    NewsComments,
  },
  setup() {
    let article = ref(null);

    (async () => {
      article.value = await fetch('/api/article', /* ... */);
    })();

    return {
      article,
      like() {
        fetch('/api/article/like', /* ... */);
      },
    };
  },
});
</script>
```

```vue
<!-- components/NewsComments.vue -->
<template>
  <div v-if="comments">
    <ul>
      <li v-for="comment in comments">
        <!-- ... -->
      </li>
    </ul>
  </div>
  <div v-else>
    Loading ...
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'NewsComments',
  setup() {
    let comments = ref(null);

    (async () => {
      comments.value = await fetch('/api/article/comments', /* ... */);
    })();

    return {
      comments,
    };
  },
});
</script>
```

```vue
<!-- components/PageNewsArticle.vue -->
<template>
  <LayoutDefault>
    <NewsArticle
      :article="article"
      @like="like"
    >
      <template #comments>
        <NewsComments :comments="comments">
      </template>
    </NewsArticle>
  </LayoutDefault>
</template>

<script>
import { defineComponent } from 'vue';

import NewsArticle from './NewsArticle.vue';
import NewsComments from './NewsComments.vue';

export default defineComponent({
  name: 'PageNewsArticle',
  components: {
    NewsArticle,
    NewsComments,
  },
  setup() {
    let article = ref(null);
    (async () => {
      article.value = await fetch('/api/article', /* ... */);
    })();

    let comments = ref(null);
    (async () => {
      comments.value = await fetch('/api/article/comments', /* ... */);
    })();

    return {
      article,
      comments,
      like() {
        fetch('/api/article/like', /* ... */);
      },
    };
  },
});
</script>
```

```vue
<!-- components/NewsArticle.vue -->
<template>
  <div v-if="article">
    <div v-html="article" />
    <button @click="$emit('like')">
      Like this article
    </button>
    <slot name="comments" />
  </div>
  <div v-else>
    Loading ...
  </div>
</template>

<script>
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'NewsArticle',
  props: {
    article: {
      default: null,
      type: String,
    },
  },
});
</script>
```

```js
// components/NewsArticle.spec.js
import { mount } from '@vue/test-utils';

import NewsArticle from './NewsArticle.vue';

test('it should render the given article markup', () => {
  let wrapper = mount(NewsArticle, {
    props: {
      article: '<h1>Hello World</h1><p>Lorem Ipsum</p>',
    },
  });

  expect(wrapper.html()).toContain('<h1>Hello World</h1>');
});

test('it should be possible to like the article', () => {
  let wrapper = mount(ProductList, {
    props: {
      article: '<h1>Hello World</h1><p>Lorem Ipsum</p>',
    },
  });

  wrapper.find('button').trigger('click');
  
  expect(wrapper.emitted()).toHaveProperty('like');
});
```
