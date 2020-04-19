<template>
    <Page actionBarHidden="true">
        <FlexboxLayout class="page">
            <StackLayout class="form">
                <Image class="logo" src="~/public/images/logo.png"></Image>
                <Label class="header" text="Restaurant Finder"></Label>

                <GridLayout rows="auto, auto, auto">
                    <StackLayout row="0" class="input-field">
                        <TextField class="input" hint="Email"
                            :isEnabled="!processing" keyboardType="email"
                            autocorrect="false" autocapitalizationType="none"
                            v-model="user.email" returnKeyType="next"
                            @returnPress="focusPassword"></TextField>
                        <StackLayout class="hr-light"></StackLayout>
                    </StackLayout>

                    <StackLayout row="1" class="input-field">
                        <TextField class="input" ref="password"
                            :isEnabled="!processing" hint="Password"
                            secure="true" v-model="user.password"
                            :returnKeyType="isLoggingIn ? 'done' : 'next'"
                            @returnPress="focusConfirmPassword"></TextField>
                        <StackLayout class="hr-light"></StackLayout>
                    </StackLayout>

                    <StackLayout row="2" v-show="!isLoggingIn"
                        class="input-field">
                        <TextField class="input" ref="confirmPassword"
                            :isEnabled="!processing" hint="Confirm password"
                            secure="true" v-model="user.confirmPassword"
                            returnKeyType="done"></TextField>
                        <StackLayout class="hr-light"></StackLayout>
                    </StackLayout>

                    <ActivityIndicator rowSpan="3" :busy="processing">
                    </ActivityIndicator>
                </GridLayout>

                <Button :text="isLoggingIn ? 'Log In' : 'Sign Up'"
                    :isEnabled="!processing" @tap="submit"
                    class="btn btn-primary m-t-20"></Button>
            </StackLayout>

            <Label class="login-label sign-up-label" @tap="toggleForm">
                <FormattedString>
                    <Span
                        :text="isLoggingIn ? 'Don’t have an account? ' : 'Back to Login'">
                    </Span>
                    <Span :text="isLoggingIn ? 'Sign up' : ''" class="bold">
                    </Span>
                </FormattedString>
            </Label>
        </FlexboxLayout>
    </Page>
</template>

<script>
    import Welcome from "./Welcome";
    export default {
        data() {
            return {
                isLoggingIn: true,
                processing: false,
                user: {
                    email: "",
                    password: "",
                    confirmPassword: ""
                }
            };
        },
        methods: {
            toggleForm() {
                this.isLoggingIn = !this.isLoggingIn;
            },

            submit() {
                if (!this.user.email && !this.user.password) {
                    this.alert(
                        "Please provide both your email address and password."
                    );
                    return;
                }

                if (!this.user.email.trim()) {
                    this.alert("Please provide a valid user email address");
                    return;
                }

                if (!this.user.password.trim()) {
                    this.alert("Please provide a valid password");
                    return;
                }

                this.processing = true;
                if (this.isLoggingIn) {
                    this.login();
                } else {
                    this.register();
                }
            },
            login() {
                this.$navigateTo(Welcome, {
                    clearHistory: true,
                    props: {
                        username: this.user.email
                    }
                });
            },
            register() {
                if (this.user.password != this.user.confirmPassword) {
                    this.alert("Your passwords do not match.");
                    this.processing = false;
                    return;
                }
                this.processing = false;
                this.alert("Your account was successfully created.");
                this.isLoggingIn = true;
            },
            focusPassword() {
                this.$refs.password.nativeView.focus();
            },
            focusConfirmPassword() {
                if (!this.isLoggingIn) {
                    this.$refs.confirmPassword.nativeView.focus();
                }
            },
            alert(message) {
                return alert({
                    title: "Restaurant Finder",
                    okButtonText: "OK",
                    message: message
                });
            }
        }
    };
</script>

<style>
    .page {
        align-items: center;
        flex-direction: column;
    }

    .form {
        margin-left: 30;
        margin-right: 30;
        flex-grow: 2;
        vertical-align: middle;
    }

    .logo {
        margin-bottom: 12;
        height: 90;
        font-weight: bold;
    }

    .header {
        horizontal-align: center;
        font-size: 25px;
        font-weight: 600;
        margin-bottom: 70;
        text-align: center;
        color: #e89607;
    }

    .input-field {
        margin-bottom: 25;
    }

    .input {
        font-size: 18;
        placeholder-color: #595959;
    }

    .input:disabled {
        /* background-color: white; */
        opacity: 0.5;
    }

    .btn-primary {
        margin: 30 5 15 5;
    }

    .login-label {
        horizontal-align: center;
        color: #595959; 
        font-size: 16;
    }

    .sign-up-label {
        margin-bottom: 20;
    }

    .bold {
        color: #000000;
    }
</style>