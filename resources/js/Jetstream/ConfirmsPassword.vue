<template>
    <span>
        <span @click="startConfirmingPassword">
            <slot />
        </span>

        <jet-dialog-modal
            id="confirmingPasswordModal"
            @close="confirmingPassword = false"
        >
            <template #title>
                {{ title }}
            </template>

            <template #content>
                {{ content }}

                <div class="mt-4">
                    <jet-input
                        type="password"
                        placeholder="Password"
                        ref="password"
                        v-model="form.password"
                        :class="{ 'is-invalid': form.error }"
                        @keyup.enter.native="confirmPassword"
                    />

                    <jet-input-error :message="form.error" />
                </div>
            </template>

            <template #footer>
                <jet-secondary-button data-dismiss="modal">
                    Nevermind
                </jet-secondary-button>

                <jet-button
                    class="ml-2"
                    @click.native="confirmPassword"
                    :class="{ 'text-black-50': form.processing }"
                    :disabled="form.processing"
                >
                    {{ button }}
                </jet-button>
            </template>
        </jet-dialog-modal>
    </span>
</template>

<script>
import JetButton from "./Button";
import JetDialogModal from "./DialogModal";
import JetInput from "./Input";
import JetInputError from "./InputError";
import JetSecondaryButton from "./SecondaryButton";

export default {
    props: {
        title: {
            default: "Confirm Password",
        },
        content: {
            default:
                "For your security, please confirm your password to continue.",
        },
        button: {
            default: "Confirm",
        },
    },

    components: {
        JetButton,
        JetDialogModal,
        JetInput,
        JetInputError,
        JetSecondaryButton,
    },

    data() {
        return {
            modal: null,

            form: this.$inertia.form(
                {
                    password: "",
                    error: "",
                },
                {
                    bag: "confirmPassword",
                }
            ),
        };
    },

    methods: {
        startConfirmingPassword() {
            this.form.error = "";
            this.modal = new Bootstrap.Modal(
                document.getElementById("confirmingPasswordModal")
            );

            axios.get(route("password.confirmation")).then((response) => {
                if (response.data.confirmed) {
                    this.$emit("confirmed");
                } else {
                    this.modal.show();
                    this.form.password = "";

                    setTimeout(() => {
                        this.$refs.password.focus();
                    }, 250);
                }
            });
        },

        confirmPassword() {
            this.form.processing = true;

            axios
                .post(route("password.confirm"), {
                    password: this.form.password,
                })
                .then((response) => {
                    this.modal.hide();
                    this.form.password = "";
                    this.form.error = "";
                    this.form.processing = false;

                    this.$nextTick(() => this.$emit("confirmed"));
                })
                .catch((error) => {
                    this.form.processing = false;
                    this.form.error = error.response.data.errors.password[0];
                });
        },
    },
};
</script>
