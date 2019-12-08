<template>
  <div class="container">
    <div>
      <Form>
        <FormItem>
          <Input v-model="value" type="text" />
        </FormItem>
        <FormItem>
          <Button @click="handleSubmit" type="primary">submit</Button>
        </FormItem>
        <FormItem>
          <span>{{ message }}</span>
        </FormItem>
      </Form>
    </div>
  </div>
</template>

<script>
import API, { graphqlOperation } from '@aws-amplify/api'

const createMessage = `mutation createMessage($message: String!) {
  createMessage(input: { message: $message }) {
    __typename
    id
    message
    createdAt
  }
}
`

const onCreateMessage = `subscription onCreateMessage {
  onCreateMessage {
    __typename
    message
  }
}`

export default {
  data() {
    return {
      message: '',
      value: ''
    }
  },
  created() {
    this.subscribe()
  },
  methods: {
    async handleSubmit(event) {
      event.preventDefault()
      event.stopPropagation()
      const message = {
        id: '',
        message: this.value,
        createdAt: ''
      }
      await API.graphql(graphqlOperation(createMessage, message))
    },
    subscribe() {
      this.subscription = API.graphql(
        graphqlOperation(onCreateMessage)
      ).subscribe({
        next: (event) => {
          if (event) {
            this.message = event.value.data.onCreateMessage.message
          }
        }
      })
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}
.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}
.links {
  padding-top: 15px;
}
</style>
