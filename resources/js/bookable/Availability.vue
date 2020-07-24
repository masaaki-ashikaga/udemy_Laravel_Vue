<template>
    <div>
        <h6 class="text-uppercase text-secondary font-weight-bolder">Check Availability</h6>
        <div class="form-row">
            <div class="form-group col-md-6">
                <label form="from">From</label>
                <input
                    type="text"
                    name="from"
                    class="form-control form-control-sm"
                    placeholder="Start date"
                    v-model="from"
                    @keyup.enter="check"
                    :class="[{'is-invalid': this.errorFor('from')}]"
                >
            </div>

            <div class="form-group col-md-6">
                <label form="to">To</label>
                <input
                    type="text"
                    name="to"
                    class="form-control form-control-sm"
                    placeholder="End date"
                    v-model="to"
                    @keyup.enter="check"
                    :class="[{'is-invalid': this.errorFor('to')}]"
                >
            </div>
            <button class="btn btn-secondary btn-block" @click="check" :disabled="loading">Check!</button>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            from: null,
            to: null,
            loading: false,
            status: null,
            errors: null
        }
    },
    methods: {
        check() {
            this.loading = true;
            this.errors = null;

            axios.get(`/api/bookables/${this.$route.params.id}/availability?from=${this.from}&to={this.to}`).then(response => {
                this.status = response.status;
            }).catch(error => {
                if(422 === error.resposne.status){
                    this.error = error.response.data.errors;
                }
                this.status = error.response.status;
            })
            .then(() => this.loading = false);
        },
        errorFor(field){
            return this.hasErrors && this.errors[field] ? this.errors[field] : null;
        },
    },
    computed: {
        hasErrors() {
            return 422 === this.status && this.errors !== null;
        },
        hasAvailability() {
            return 200 === this.status;
        },
        noAvailability() {
            return 400 === this.status;
        }
    }
}
</script>

<style scoped>
    label {
        font-size: 0.7rem;
        text-transform: uppercase;
        color: gray;
        stroke-width: bolder;
    }

    .is-invalid{
        border-color: #b22222;
        background-image: none;
    }
</style>