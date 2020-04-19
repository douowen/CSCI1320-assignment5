<template>
    <Page>
        <ActionBar title="Restaurant Finder"></ActionBar>

        <StackLayout>
            <Label class="body m-20" :text="getMessage()" textWrap="true">
            </Label>
            <Button class="btn btn-primary" text="Show Restaurants" @tap="goToRestaurants">
            </Button>
            <Button class="btn btn-primary" text="Log out" @tap="logout">
            </Button>
        </StackLayout>
    </Page>
</template>

<script>
    import Login from "./Login";
    import Restaurants from "./Restaurants";

    export default {
        props: ["username"],
        data() {
            return {
                message: "test"
            };
        },
        methods: {
            logout() {
                this.$navigateTo(Login, {
                    clearHistory: true
                });
            },
            getMessage() {
                return (
                    "Welcome " +
                    this.username +
                    "! Tap the button below to find the restaurants!"
                );
            },
            goToRestaurants() {
                this.$navigateTo(Restaurants, {
                    clearHistory: true,
                    props: {
                        username: this.username
                    }
                });
            }
        }
    };
</script>

<style>
</style>