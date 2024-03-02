<script setup>
import OpenAI from 'openai'
import { ref } from 'vue'

const key = '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';
const openai = new OpenAI({ apiKey: key, dangerouslyAllowBrowser: true })

const prompt = ref('')
const result = ref('')
const imageResult = ref('')
const loading = ref(false)

let query = async () => {
  result.value = '';
  imageResult.value = '';
  loading.value = true

  const completion = await openai.chat.completions.create({
    messages: [{ role: 'system', content: prompt.value }],
    model: 'gpt-3.5-turbo'
  })
  
  loading.value = false
  result.value = completion.choices[0].message.content
  console.log(completion.choices[0])
}

let queryImage = async () => {
  result.value = '';
  imageResult.value = '';
  loading.value = true

  const image = await openai.images.generate({
    model: "dall-e-3",
    prompt: prompt.value,
    n: 1,
    size: "1024x1024",
  });

  console.log(image.data[0].url);

  imageResult.value = image.data[0].url;
  loading.value = false
}
</script>

<template>
  <v-app id="inspire">
    <v-app-bar class="px-3" flat density="compact">
      <v-avatar color="grey-darken-1" class="hidden-md-and-up" size="32"></v-avatar>

      <v-spacer></v-spacer>

      <v-tabs centered color="grey-darken-2">
        <v-tab v-for="link in []" :key="link" :text="link"></v-tab>
      </v-tabs>
      <v-spacer></v-spacer>

      <v-avatar class="hidden-sm-and-down" color="grey-darken-1" size="32"></v-avatar>
    </v-app-bar>

    <v-main class="bg-grey-lighten-3">
      <v-container>
        <v-row>
          <v-col cols="12" md="2">
<!--            <v-sheet rounded="lg" min-height="268">-->
<!--              &lt;!&ndash;  &ndash;&gt;-->
<!--            </v-sheet>-->
          </v-col>

          <v-col cols="12" md="8">
            <v-col cols="12" md="12">
              <v-sheet min-height="40vh" rounded="lg">
                <v-textarea label="Prompt" variant="solo" v-model="prompt" v-on:keyup.enter="query()"></v-textarea>
                <v-btn prepend-icon="$vuetify" @click="query()" min-width="100%"> TEXT </v-btn>
                <v-btn prepend-icon="$vuetify" @click="queryImage()" min-width="100%"> IMAGE </v-btn>
              </v-sheet>
            </v-col>

            <v-col cols="12" md="12">
              <v-sheet min-height="70vh" rounded="lg" class="my-3 mx-3 py-5 px-5">
                <div class="text-center">
                  <v-progress-linear
                    v-if="loading"
                    class="my-5"
                    indeterminate="true"
                    color="cyan"
                  ></v-progress-linear>
                </div>
                <img v-if="imageResult" class="text-left" :src="imageResult" width="100%"/>
                <p v-if="result" class="text-left" v-html="result"></p>
              </v-sheet>
            </v-col>
          </v-col>

          <v-col cols="12" md="2">
<!--            <v-sheet rounded="lg" min-height="268">-->
<!--              &lt;!&ndash;  &ndash;&gt;-->
<!--            </v-sheet>-->
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>
