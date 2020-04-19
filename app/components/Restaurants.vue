<template>
    <Page>
        <ActionBar title="Restaurant Finder"></ActionBar>
        <StackLayout class="restaurant-container">
            <Button class="btn btn-primary" text="Back" @tap="goBack">
            </Button>
            <SearchBar hint="Search by restaurant's name" :text="searchText"
                @submit="onSearchSubmit" @clear="onSearchClear" />

            <ListView for="res in restaurants" style="height:1250px">
                <v-template>
                    <FlexboxLayout flexDirection="row">
                        <Image :src="res.icon"
                            class="thumb img-circle res-image" />
                        <Label :text="res.name" class="t-12"
                            style="width: 60%" />
                    </FlexboxLayout>
                </v-template>
            </ListView>
        </StackLayout>
    </Page>
</template>

<script>
    const Geolocation = require("nativescript-geolocation");
    const http = require("tns-core-modules/http");
    import Welcome from "./Welcome";

    export default {
        // props: ["username"],
        data() {
            return {
                restaurants: [],
                searchText: "",
                radius: 1500
            };
        },
        created() {
            this.getMyRestaurants();
        },
        methods: {
            onSearchSubmit(args) {
                this.getSearchResult(args.object.text.trim());
            },
            onSearchClear(args) {
                setTimeout(() => {
                    args.object.dismissSoftInput();
                }, 0);
                this.getMyRestaurants();
            },
            getMyRestaurants() {
                Geolocation.enableLocationRequest();
                Geolocation.getCurrentLocation({
                    desiredAccuracy: 100,
                    updateDistance: 0.1,
                    timeout: 20000
                }).then(
                    loc => {
                        if (loc) {
                            const key =
                                "AIzaSyAFqxopqnykLvfDApFgVEyRxMOlQJ04_14";
                            const request = "nearbysearch";
                            const output = "json";
                            const location = loc.latitude + "," + loc
                                .longitude;
                            const parameters =
                                "location=" +
                                location +
                                "&radius=" +
                                this.radius +
                                "&type=restaurant";
                            const url =
                                `https://maps.googleapis.com/maps/api/place/${request}/${output}?${parameters}&key=${key}`;

                            http.request({
                                url: url,
                                method: "GET"
                            }).then(res => {
                                this.parseResponse(res);
                            });
                        }
                    },
                    function(e) {
                        console.log("Error: " + e.message);
                    }
                );
            },
            parseResponse(response) {
                const res = response.content.toJSON().results;
                let number = Math.min(10, res.length);
                this.restaurants = res.slice(0, number);
            },
            getSearchResult(keyword) {
                let temp = [];
                this.restaurants.forEach(item => {
                    if (item.name.includes(keyword)) {
                        temp.push(item);
                    }
                });

                this.restaurants = temp;
                if (!temp.length) {
                    this.alert();
                }
            },
            goBack() {
                this.$navigateTo(Welcome, {
                    clearHistory: true,
                    props: {
                        username: this.username
                    }
                });
            },
            alert() {
                return alert({
                    title: "Restaurant Finder",
                    okButtonText: "OK",
                    message: "Cannot find the restaurant on your search"
                });
            }
        }
    };
</script>

<style>
    .res-image {
        width: 200px;
        height: 200px;
    }
</style>