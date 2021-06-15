<template>
  <div class="hello">
    <div class="container">
      <div class="row">
        <div class="col-lg-3 col-sm-3"><b>Nunber Of Results :</b>{{num_results}}</div>
        <div class="col-lg-5 col-sm-5"></div>
        <div class="col-lg-4 col-sm-4" >
          <select
            class="form-select form-select-lg mb-3 form-control"
            aria-label="Default select example"
            v-model="search"
            @change="getData"
          >
            <option value="1">1 day</option>
            <option value="7">7 days</option>
            <option value="30">30 days</option>
          </select>
        </div>
      </div>
      <div class="row">
        <div class="col-lg-4 col-sm-4" style="width: 25rem" v-for="post in info">
          <div class="card">
            <img class="card-img-top" :src="post.image" alt="Card image cap" />
            <div class="card-body">
              <a
                :href="post.url"
                target="_blank"
                ><h5 class="card-title">{{ post.title }}</h5>
              </a>
              <p class="card-text">{{ post.abstract }}</p>
              <p class="card-text">
                <b>Published Date</b> :{{ post.published_date }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      info: [],
      search: 1,
      startDate: null,
      endDate: null,
      num_results: "",
    };
  },
  mounted() {},
  methods: {
    getImage(data) {
      console.log(data);
      var url;
      if (data != undefined) {
        var parseData = JSON.parse(JSON.stringify(data));
        url = parseData["media-metadata"][2].url;
      } else {
        url = "http://placehold.it/210x140?text=N/A";
      }
      return url;
    },
    async getData() {
      try {
        const url =
          "https://api.nytimes.com/svc/mostpopular/v2/viewed/" +
          this.search +
          ".json?api-key=vsIuBLZYxaCgo2grLGGtTqkjiiGl27mL";
        const response = await axios.get(url);
        const results = response.data.results;
        console.log(results);
        this.info = results.map((post) => ({
          title: post.title,
          abstract: post.abstract,
          url: post.url,
          image: this.getImage(post.media[0]),
          //image: post.media[0].caption,
          published_date: post.published_date,
        }));
        this.num_results = response.data.num_results;
      } catch (err) {
        if (err.response) {
          // client received an error response (5xx, 4xx)
          console.log("Server Error:", err);
        } else if (err.request) {
          // client never received a response, or request never left
          console.log("Network Error:", err);
        } else {
          console.log("Client Error:", err);
        }
      }
      console.log(this.info);
    },
  },
  created() {
    this.getData();
  },
};
</script>

