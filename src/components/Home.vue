<template>
  <v-container class="fill-height">
    <v-responsive class="align-centerfill-height mx-auto">
      <v-row>
        <v-col cols="12">
          <v-form v-if="!state.done">
            <v-card
              title="Registration"
              variant="flat"
            >
              <v-card-text>
                <v-text-field
                  v-model="state.first_name"
                  :counter="10"
                  :error-messages="v$.first_name.$errors.map((e) => e.$message)"
                  label="First Name"
                  required
                  @blur="v$.first_name.$touch"
                  @input="v$.first_name.$touch"
                ></v-text-field>

                <br />

                <v-text-field
                  v-model="state.last_name"
                  :counter="10"
                  :error-messages="v$.last_name.$errors.map((e) => e.$message)"
                  label="Last Name"
                  required
                  @blur="v$.last_name.$touch"
                  @input="v$.last_name.$touch"
                ></v-text-field>

                <br />

                <v-text-field
                  v-model="state.email"
                  :error-messages="v$.email.$errors.map((e) => e.$message)"
                  label="E-mail"
                  required
                  @blur="v$.email.$touch"
                  @input="v$.email.$touch"
                ></v-text-field>

                <br />

                <v-checkbox
                  v-model="state.checkbox"
                  :error-messages="v$.checkbox.$errors.map((e) => e.$message)"
                  label="Yes you do?"
                  required
                  @blur="v$.checkbox.$touch"
                  @change="v$.checkbox.$touch"
                ></v-checkbox>

                <v-label>Signature</v-label>

                <Vue3Signature
                  ref="signature1"
                  :sigOption="state.option"
                  :w="'320px'"
                  :h="'400px'"
                  style="border: 1px solid #000"
                ></Vue3Signature>
              </v-card-text>

              <v-card-actions>
                <v-btn
                  class="me-4"
                  @click="submit"
                >
                  submit
                </v-btn>

                <v-btn @click.prevent="clear">Clear</v-btn>
              </v-card-actions>
            </v-card>
          </v-form>

          <div v-else>
            <v-carousel
              height="100%"
              show-arrows="hover"
              cycle
              hide-delimiter-background
            >
              <v-carousel-item>
                <v-card class="text-center">
                  <v-card-title> CONGRATS TO THE NEWLYWEDS! </v-card-title>

                  <v-card-subtitle> Eme lang </v-card-subtitle>

                  <v-card-text>
                    <img
                      :src="certificateImage"
                      alt="Certificate Image"
                      width="200"
                    />

                    <v-divider></v-divider>
                    <br />

                    <v-row>
                      <v-col cols="12">
                        Certificate of
                        <br />
                        <h3>Marriage</h3>
                      </v-col>

                      <v-col cols="6">
                        <v-col cols="12">
                          <img
                            :src="sign"
                            alt=""
                            width="100"
                          />
                        </v-col>

                        <v-col cols="12"> Danniel Libor </v-col>
                      </v-col>

                      <v-col cols="6">
                        <v-col cols="12">
                          <img
                            :src="state.sign"
                            alt=""
                            width="200"
                            height="100"
                          />
                        </v-col>

                        <v-col cols="12">
                          {{ state.first_name }} {{ state.last_name }}
                        </v-col>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-carousel-item>

              <v-carousel-item>
                <v-card
                  border
                  color="pink"
                  rounded="xl"
                  variant="tonal"
                >
                  <v-card-text class="mx-12 px-12 py-12">
                    <v-row>
                      <v-col cols="12">
                        <h1>Happy First Valentine’s Day, Lovey!</h1>
                        <br />
                        <h3>
                          To my sweetest nurse with the most adorable cheeks.
                          Your baby face might fool the world, but you’ve got my
                          heart on life support.
                        </h3>
                        <br />
                        <h3>
                          You are my favorite person, today and always. 💖
                        </h3>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-carousel-item>
            </v-carousel>
          </div>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script setup>
  import certificateImage from '@/assets/image.png'
  import sign from '@/assets/sign.png'
  import { reactive, ref, getCurrentInstance } from 'vue'
  import { useVuelidate } from '@vuelidate/core'
  import { email, required, helpers } from '@vuelidate/validators'
  import VueConfetti from 'vue-confetti'

  const { appContext } = getCurrentInstance()
  appContext.app.use(VueConfetti)

  const initialState = {
    first_name: '',
    last_name: '',
    email: '',
    checkbox: null,
  }

  const state = reactive({
    ...initialState,
    option: {
      penColor: 'rgb(0, 0, 0)',
      backgroundColor: 'rgb(255,255,255)',
    },
    done: false,
  })

  const rules = {
    first_name: {
      required: helpers.withMessage('This field cannot be empty', required),
    },
    last_name: {
      required: helpers.withMessage('This field cannot be empty', required),
    },
    email: {
      required: helpers.withMessage('This field cannot be empty', required),
      email,
    },
    checkbox: {
      required: helpers.withMessage('You must agree to continue', required),
    },
  }

  const v$ = useVuelidate(rules, state)

  const signature1 = ref(null)

  const submit = async () => {
    await v$.$validate

    if (v$.value.$invalid) {
      v$.value.$touch()
      return
    }

    state.sign = signature1.value.save('image/jpeg')
    state.done = true

    appContext.config.globalProperties.$confetti.start({
      particles: [
        {
          type: 'heart',
        },
      ],
      defaultColors: ['red', 'pink', '#ba0000'],
      particlesPerFrame: 0.5,
      defaultDropRate: 5,
    })
  }

  const clear = () => {
    signature1.value.clear()
  }
</script>
