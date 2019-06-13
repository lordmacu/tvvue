<i18n src="./i18n/index.json"></i18n>

<template>



    <div class="container-fluid" >

        <loading :active.sync="isLoading" 
            :can-cancel="true" 
            :on-cancel="onCancel"
            :is-full-page="fullPage"></loading>

        <div class="row">
            <div class="col-3" v-if="!fullscreenI">
                <div class="preview-movie row" v-if="preview">

                    <div  class="controls col-lg-12 d-none d-sm-block">
                        <table>
                            <tr>
                                <td></td>
                                <td><button v-on:click="jumpUp()"  >Arriba</button></td>
                                <td></td>
                            </tr>
                            <tr>
                                <td><button v-on:click="jumpStep(2)" >Izquierda</button></td>
                                <td class="container-play"><button class="play-button" v-on:click="loadVideo()" >Play</button></td>
                                <td><button v-on:click="jumpStep(1)" > Derecha</button></td>
                            </tr>
                            <tr>
                                <td> <button v-on:click="back()"><- Atras</button></td>
                                <td> <button v-on:click="jumpDown()">Abajo</button></td>
                                <td></td>
                            </tr>
                        </table>


                    </div>


                    <div class="col-lg-12 description-container-movie">
                        <h1>{{preview.title}}</h1>
                        <span>Año - {{preview.year}}</span> - 
                        <span>Duración - {{preview.infoTime}}</span> - 
                        <span>Calidad - {{preview.infoQlty}}</span> 
                        <p v-html="preview.description"></p>
                        <br/>
                        <div class="row">
                            <div class="col-12 container-button-video" v-for="source in sourcesVideo">

                                <button class="btn btn-default  btn-light" v-on:click="showVideoIdentifier(source.identifier)">
                                    {{source.nameResource}}
                                </button>
                            </div>
                        </div>

                    </div>


                </div>
            </div>

            <div class="col-lg-9">
                <div  id="movies-container"  v-on:mouseleave="mouseInVideoContainer(2)"  v-on:mouseover="mouseInVideoContainer(1)" nv-scope="menu" class="row movies-container" nv-scope-current v-if='movies.length>0'>

                    <div class="col-12 container-categories">
                        <div class="row">
                            <div class="col-12 col-md-7 col-lg-7">
                                <button class="btn btn-default btn-light" v-on:click="latest()" >Últimas</button>
                                <button class="btn btn-default btn-light" v-on:click="newMovies()">Estrenos</button>
                                <button class="btn btn-default btn-light" v-on:click="ranking()">Ranking</button>
                                <button class="btn btn-default btn-light" v-on:click="popular()">Mas vistas</button>
                                <button class="btn btn-default btn-light" v-on:click="series()">Series</button>

                            </div>

                            <div class="col-12 col-md-5 col-lg-5">
                                <br/>

                                <div class="input-group mb-3">
                                    <input type="text" v-model="searchText" class="form-control" placeholder="Buscar por nombre">

                                    <div class="input-group-prepend">
                                        <button class="btn btn-outline-secondary" type="button" id="button-addon1" v-on:click="searchMovie">Buscar</button>
                                    </div>
                                </div>
                            </div>
                        </div>



                    </div>
                    <div nv-el  nv-el-current :class="checkZoom(movies[0])" v-popover:tooltip="'This is a string value'"  v-on:click="overClick(movies[0],0)"  :name="'element_'+0" :id="'element_'+0" :datai="0"  :style="'background-image:url('+movies[0].image+')'" >

                         <div class="content-movie row"  >
                            <span>{{movies[0].infoQlty}}</span>
                        </div>

                    </div>


                    <div v-for="(movie,index) in movies"  :class="checkZoom(movie)" :name="'element_'+index"  v-on:click="overClick(movie,index)"  v-if="index!=0"  :id="'element_'+index"    :datai="index"   nv-el  :style="'background-image:url('+movie.image+')'" >
                         <div class="content-movie row ss">
                            <span>{{movie.infoQlty}}</span>
                        </div>
                    </div>

                    <div class="col-12 load-more-container">
                        <button class="btn btn-default btn-light" v-on:click="loadMore">Cargar mas</button>
                    </div>



                </div>

                <div class="container-video " v-if="frameVideo">
                    <div class="row">

                        <div class="col-12 container-video-frame" >
                            <iframe id="myvideo" :class="fullscreenIframe()"  @error="loadedErrorVideo"    v-on:load="loadedVideo" width="80%" height="500" :data-src="frameVideo" :src="frameVideo" frameborder="0" allowfullscreen="true"></iframe>
                            <hr/>
                            <!--<button v-if="!fullscreenI" v-on:click="fullscreen" class="btn btn-default btn-light">FullScreen</button>  -->          

                        </div>
                    </div>


                </div>
            </div>
        </div>




        <div class="spaceload"></div>
    </div>

</template>

<script>

    import Loading from 'vue-loading-overlay';
    // Import stylesheet
    import 'vue-loading-overlay/dist/vue-loading.css';
    export default {
        name: 'Home',
        data() {
            return {
                movies: [],
                preview: null,
                jumpValue: 0,
                jumpStepValue: 0,
                sourcesVideo: [],
                players: [],
                frameVideo: null,
                paginator: 1,
                category: 0,
                searchText: null,
                fullscreenI: false,
                oldX: 0,
                oldY: 0,
                turnOnArrowsControl: true,
                isLoading: false,
                fullPage: true
            }
        },
        components: {
            Loading
        },
        created() {
            if (this.isSmartTV()) {
                var bodyElement = document.querySelector("body");
                //bodyElement.addEventListener("mousemove", this.captureMouseMove, false);

            }



            window.addEventListener('keydown', (e) => {
                if (e.keyCode == 54) {
                    this.jumpStep(1)
                } else if (e.keyCode == 52) {
                    this.jumpStep(2)
                } else if (e.keyCode == 56) {
                    this.jumpDown();
                } else if (e.keyCode == 48) {
                    this.jumpDown();
                } else if (e.keyCode == 50) {
                    this.jumpUp();
                } else if (e.keyCode == 53) {
                    this.loadVideo();
                } else if (e.keyCode == 55) {
                    this.back();
                }





            });


            document.onkeydown = function (evt) {
                evt = evt || window.event;
                var isEscape = false;
                if ("key" in evt) {
                    isEscape = (evt.key == "Escape" || evt.key == "Esc");
                } else {
                    isEscape = (evt.keyCode == 27);
                }
                if (isEscape) {
                    alert("Escape");
                }
            };

            document.body.addEventListener('nv-left', (event) => {


                this.tabMovie(event, false);

            });

            document.body.addEventListener('nv-up', (event) => {
                this.tabMovie(event, true);

            });

            document.body.addEventListener('nv-down', (event) => {
                this.tabMovie(event, true);

            });



            document.body.addEventListener('nv-right', (event) => {
                this.tabMovie(event, false);

            });






            this.paginator = 1;

            if (this.$route.query.url) {
                this.category = this.$route.query.url;

            }
            this.loadHome();

        },
        computed: {

        },
        methods: {
            loadedErrorVideo() {
                this.isLoading = false;
                alert("error cargando video");
            },
            loadedVideo() {
                this.isLoading = false;
            },
            mouseInVideoContainer(enterMouse) {
                if (enterMouse == 1) {
                    this.turnOnArrowsControl = true;
                } else {
                    this.turnOnArrowsControl = false;
                }
            },
            onCancel() {

            },
            isSmartTV() {
                return navigator.userAgent.search(/TV/i) >= 0;
            },

            captureMouseMove($event) {
                let directionX = 0, directionY = 0, diffX = 0, diffY = 0;
                if ($event.pageX < this.oldX) {
                    directionX = "left"
                    diffX = this.oldX - $event.pageX;
                } else if ($event.pageX > this.oldX) {
                    directionX = "right"
                    diffX = $event.pageX - this.oldX;
                }

                if ($event.pageY < this.oldY) {
                    directionY = "top"
                    diffY = this.oldY - $event.pageY;
                } else if ($event.pageY > this.oldY) {
                    directionY = "bottom";
                    diffY = $event.pageY - this.oldY;
                }

                this.oldX = $event.pageX;
                this.oldY = $event.pageY;

                if (this.turnOnArrowsControl == true) {
                    if (directionX == "left") {
                        this.jumpStep(2)
                    } else if (directionX == "right") {
                        this.jumpStep(1)
                    } else if (directionY == "top") {
                        this.jumpUp()
                    } else if (directionY == "bottom") {
                        this.jumpDown()
                    }
                }


            },

            fullscreenIframe() {
                if (this.fullscreenI) {
                    return "fullscreen";
                }
            },
            fullscreen() {
                this.fullscreenI = true;


                try {

                    var elem = document.documentElement;

                    if (elem.requestFullscreen) {
                        elem.requestFullscreen();
                    } else if (elem.mozRequestFullScreen) { /* Firefox */
                        elem.mozRequestFullScreen();
                    } else if (elem.webkitRequestFullscreen) { /* Chrome, Safari and Opera */
                        elem.webkitRequestFullscreen();
                    } else if (elem.msRequestFullscreen) { /* IE/Edge */
                        elem.msRequestFullscreen();
                    }



                } catch (err) {
                    alert(err.message);
                }


            },
            searchMovie() {

                this.paginator = 1;
                this.movies = [];

                if (!this.searchText) {
                    this.category = 0;

                    this.loadHome();
                    return false;
                }
                this.movies = [];
                this.isLoading = true;
                this.axios.get("https://www.alquilerdirecto.com.ar/searchMovie?search=" + this.searchText, ).then((response) => {

                    var responseHtml = $(response.data);
                    var listMovies = responseHtml.find(".MovieList li");

                    for (var i = 0; i < listMovies.length; i++) {
                        var item = $(listMovies[i]);
                        var link = item.find("a").attr("href");
                        var title = item.find(".Title").html();
                        var year = item.find(".Year").html();
                        
                        
                        
                        var image = item.find("figure img").attr("data-src")
                        if(!!image){
                            var image = image.replace("-160x242", "");
                            var image = image.replace("-55x85", "");

                        } 
                        var infoTime = item.find(".Info .Time").html();
                        var infoQlty = item.find(".Info .Qlty").html();
                        var description = item.find(".Description").html();

                        this.movies.push({
                            link: link,
                            title: title,
                            year: year,
                            image: image,
                            infoTime: infoTime,
                            infoQlty: infoQlty,
                            description: description,
                            current: false,
                            index: i
                        });

                    }

                    this.preview = this.movies[0];

                    setTimeout(() => {
                        navigation.refresh();
                        this.isLoading = false;

                    }, 1000);

                });
            },

            latest() {
                this.paginator = 1;
                this.movies = [];
                this.category = 0;
                this.loadHome();
            },
            newMovies() {
                this.movies = [];

                this.paginator = 1;
                this.category = "estrenos";
                // this.loadHome();
                window.open("/tvvue/dist/#/?url=estrenos")

            },
            ranking() {
                this.movies = [];

                this.paginator = 1;
                this.category = "peliculas-mas-valoradas";
                window.open("/tvvue/dist/#/?url=peliculas-mas-valoradas")

                //this.loadHome();
            },
            popular() {
                this.movies = [];

                this.paginator = 1;
                this.category = "peliculas-mas-vistas";
                window.open("/tvvue/dist/#/?url=peliculas-mas-vistas")

                // this.loadHome();
            },
            series() {
                this.movies = [];

                this.paginator = 0;
                this.category = "serie";
                window.open("/tvvue/dist/#/?url=serie")

                // window.location.href="?url=serie"; 
                //this.$route.router.go("?url=serie");

                //  this.loadHome();
            },

            loadHome() {


                this.isLoading = true;

                this.axios.get("https://alquilerdirecto.com.ar/getHomeMovies?paginator=" + this.paginator + "&category=" + this.category, ).then((response) => {

                    var responseHtml = $(response.data);
                    var listMovies = responseHtml.find(".MovieList li");

                    for (var i = 0; i < listMovies.length; i++) {


                        var item = $(listMovies[i]);
                        var link = item.find("a").attr("href");
                        var title = item.find(".Title").html();
                        var year = item.find(".Year").html();

                var image = item.find("figure img").attr("data-src")

                                if(!!image){
                                    image=image.replace("-160x242", "");;
                                    var image = image.replace("-55x85", "");

                                }
                        var infoTime = item.find(".Info .Time").html();
                        var infoQlty = item.find(".Info .Qlty").html();
                        var description = item.find(".Description").html();

                        if (infoQlty) {

                            if (link != "#") {
                                this.movies.push({
                                    link: link,
                                    title: title,
                                    year: year,
                                    image: image,
                                    infoTime: infoTime,
                                    infoQlty: infoQlty,
                                    description: description,
                                    current: false,
                                    index: i
                                });
                            }

                        }




                    }

                    this.preview = this.movies[0];

                    setTimeout(() => {
                        navigation.refresh();
                        this.isLoading = false;
                    }, 1000);



                })
            },
            back() {
                this.frameVideo = null;
            },
            loadMore() {
                this.paginator++;
                this.loadHome();
            },

            loadVideo() {
                this.loadUrl(this.preview.link);
            },

            getVideoFrame(url) {
                this.frameVideo = url;

                return false;
                var url = url.split("file=");
                if (url.length == 2) {
                    this.frameVideo = url[1];
                } else {
                    this.frameVideo = url[0];
                }

            },
            loadUrl(url) {

                this.isLoading = true;


                this.axios.get("https://alquilerdirecto.com.ar/getUrl?url=" + encodeURI(url), ).then((response) => {
                    var responseHtml = $(response.data);

                    var players = responseHtml.find(".TPlayerTb");



                    var episodes = responseHtml.find(".all-episodes");





                    if (players.length > 0) {
                        for (var ifr = 0; ifr < players.length; ifr++) {

                            var iframe = $(players[ifr]);

                            this.players.push({id: iframe.attr("id"), frame: iframe.find("iframe").attr("data-src")})
                        }

                        var videosResources = responseHtml.find(".open_submenu ul li");
                        this.sourcesVideo = [];
                        for (var i = 0; i < videosResources.length; i++) {

                            var resource = $(videosResources[i]);
                            var identifier = resource.attr("data-tplayernv");
                            var nameResource = resource.find("span span").html();
                            var image = resource.find("img").attr("data-src");

                            this.sourcesVideo.push({identifier: identifier, nameResource: nameResource, image: image});


                        }


                        this.showVideoIdentifier(this.sourcesVideo[0].identifier);


                    } else {

                        if (episodes.length > 0) {
                            this.isLoading = false;

                    this.movies = [];
                            for (var s = 0; s < episodes.length; s++) {
                                var listEpisodes = $(episodes[s]).find("li");
                                for (var e = 0; e < listEpisodes.length; e++) {
                                    var item = $(listEpisodes[e]);
                                    var link = item.find("a").attr("href");
                                    var title = item.find(".Title").html();
                                    var year = "";
                                    var image = item.find("figure img").attr("data-src").replace("-160x242", "");
                                    var image = image.replace("-55x85", "");
                                    var infoTime = "";
                                    var infoQlty = "T " + (s + 1) + " - " + title;
                                    var description = "";
                                   

                                    this.movies.push({
                                        link: link,
                                        title: title,
                                        year: year,
                                        image: image,
                                        infoTime: infoTime,
                                        infoQlty: infoQlty,
                                        description: description,
                                        current: false,
                                        index: i
                                    });
                                }
                            }

                            this.preview = this.movies[0];

                            setTimeout(() => {
                                navigation.refresh();
                                this.isLoading = false;

                            }, 1000);
                        }



                        /*    for (var i = 0; i < listMovies.length; i++) {
                         var item = $(listMovies[i]);
                         var link = item.find("a").attr("href");
                         var title = item.find(".Title").html();
                         var year = item.find(".Year").html();
                         var image = item.find("figure img").attr("src").replace("-160x242", "");
                         var image = image.replace("-55x85", "");
                         var infoTime = item.find(".Info .Time").html();
                         var infoQlty = item.find(".Info .Qlty").html();
                         var description = item.find(".Description").html();
                         
                         this.movies.push({
                         link: link,
                         title: title,
                         year: year,
                         image: image,
                         infoTime: infoTime,
                         infoQlty: infoQlty,
                         description: description,
                         current: false,
                         index: i
                         });
                         
                         }
                         
                         */

                    }



                })
            },
            showVideoIdentifier(source) {
                this.isLoading = true;

                for (var i = 0; i < this.players.length; i++) {
                    if (this.players[i].id == source) {
                        this.getVideoFrame(this.players[i].frame);
                    }
                }

            },
            jumpStep(step) {
                if (step == 1) {

                    this.jumpValue = this.preview.index + 1;
                } else {
                    if (this.jumpValue > 0) {
                        this.jumpValue = this.preview.index - 1;
                    }
                }



                this.jump("element_" + this.jumpValue);
                this.preview = this.movies[this.jumpValue];

            },
            checkZoom(movie) {
                if (movie) {
                    if (this.preview) {
                        if (movie.index == this.preview.index) {
                            return "col-md-3 col-lg-3 col-12 movie-container  zoom";
                        } else {
                            return "col-md-3 col-lg-3 col-12 movie-container";
                        }
                    }

                }

            },
            overmouse(movie, index) {

                this.preview = movie;

            },
            overClick(movie, index) {

                this.preview = movie;

                this.loadVideo();
            },
            jumpUp() {
                if (this.jumpValue > 0) {
                    this.jumpValue = this.jumpValue - 4;
                    this.jump("element_" + this.jumpValue);
                    this.preview = this.movies[this.jumpValue];
                }


            },
            jumpDown() {
                this.jumpValue = this.jumpValue + 4;

                this.jump("element_" + this.jumpValue);
                this.preview = this.movies[this.jumpValue];

            },
            jump(h) {
                var top = document.getElementById(h).offsetTop;
                var top = top - 60;
                window.scrollTo(0, top);

            },
            tabMovie(event, anchor) {


                var element = event.target.id.split("_");

                this.movies[element[1]].current = false;

                var current = $(".nv-scope-current").find(".nv-el-current").attr("datai");

                if (this.movies[current]) {
                    this.movies[current].current = true;
                    this.preview = this.movies[current];
                    if (anchor) {
                        this.jump("element_" + current);
                    }

                }
            }
        }
    }




</script>

<style lang="scss">
    .Home {
        padding: 50px;
        color: $color-primary;
    }
</style>
