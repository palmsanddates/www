<script>
import Navbar from '@/components/navbar';
import Service from '@/components/services';
import Features from '@/components/features';
import Contact from '@/components/contact';
import Footer from '@/components/footer';

import {
  BSpinner,
  BForm,
  BFormGroup,
  BFormInput,
  BFormInvalidFeedback,
} from 'bootstrap-vue';
import { validationMixin } from 'vuelidate';
import { required, email } from 'vuelidate/lib/validators';

/**
 * Index-1
 */
export default {
  mixins: [validationMixin],
  components: {
    Navbar,
    Service,
    Features,
    Contact,
    Footer,
    BSpinner,
    BForm,
    BFormGroup,
    BFormInvalidFeedback,
    BFormInput,
  },
  data() {
    return {
      isSending: false,
      form: {
        emailSubscribe: '',
      },
    };
  },
  validations: {
    form: {
      emailSubscribe: {
        required,
        email,
      },
    },
  },
  methods: {
    validateState(name) {
      const { $dirty, $error } = this.$v.form[name];
      return $dirty ? !$error : null;
    },
    resetForm() {
      this.form = {
        emailSubscribe: null,
      };

      this.$nextTick(() => {
        this.$v.$reset();
      });
    },
    async onSubmit() {
      try {
        this.isSending = true;
        this.$v.form.$touch();
        if (this.$v.form.$anyError) {
          this.isSending = false;
          return;
        }
        await this.axios.post('/mail/subscribe', {
          email: this.form.emailSubscribe,
        });
        this.isSending = false;
        this.resetForm();
      } catch (err) {
        this.isSending = false;
      }
    },
  },
};
</script>

<template>
  <div>
    <Navbar />
    <div v-scroll-spy>
      <!-- Hero Start -->
      <section
        class="hero-1-bg"
        :style="{
          'background-image':
            'url(' + require('@/assets/images/hero-1-bg-img.png') + ')',
          'z-index': '-1',
        }"
        id="home"
      >
        <div class="container">
          <div class="row align-items-center justify-content-center">
            <img
              src="@/assets/images/hero-1.webp"
              class="img-responsive"
              width="900"
              style="position: absolute; z-index: 0; right: 10%; top: 25%;"
            />
            <div class="col-lg-8 mr-auto" style="position: relative;">
              <div class="card p-4 glass">
                <div class="card-body">
                  <h1 class="hero-1-title text-dark font-weight-bold mb-4">
                    Dorm Community Made Simple
                  </h1>
                  <div class="w-75 mb-5 mb-lg-0">
                    <p class="text-muted mb-5 pb-5 font-size-17">
                      Made for and by students. We're here to change the way we
                      socialize in our dorm residence.
                    </p>
                    <p class>Want to stay in the loop?</p>
                    <div class="subscribe-form">
                      <b-form @submit.stop.prevent="onSubmit">
                        <b-form-group>
                          <b-form-input
                            placeholder="Enter  email..."
                            v-model.trim.lazy="$v.form.emailSubscribe.$model"
                            :state="validateState('emailSubscribe')"
                            aria-describedby="input-email-subscribe"
                          />
                          <button type="submit" class="btn btn-primary">
                            <div class="text-center" v-if="!isSending">
                              Subscribe
                            </div>
                            <div v-else>
                              <b-spinner small label="Loading..."></b-spinner>
                            </div>
                          </button>
                          <b-form-invalid-feedback id="input-email-subscribe"
                            >Please enter a valid email
                            address.</b-form-invalid-feedback
                          >
                        </b-form-group>
                      </b-form>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
      <!-- Hero End -->
      <Service />
      <Features />
      <Contact />
      <Footer />
    </div>
  </div>
</template>
