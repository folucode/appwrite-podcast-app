<template>
  <div class="container">
    <div class="child">
      <Podcast
        v-for="podcast in podcasts"
        :key="podcast.id"
        :title="podcast.title"
        :date="podcast.date"
        :source="podcast.link"
        :name="podcast.name"
      />
    </div>

    <div class="child">
      <form>
        <label for="title">Title</label>
        <input
          type="text"
          name="title"
          placeholder="Podcast title..."
          v-model="title"
        />

        <label for="country">File</label>
        <input id="file" type="file" />

        <button type="submit" value="Submit" @click.prevent="uploadPodcast">
          Upload
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import { Client, Account, Databases, Storage, Query } from "appwrite";
import { faker } from "@faker-js/faker";

const client = new Client();
client
  .setEndpoint("http://localhost/v1") // Our Appwrite Endpoint
  .setProject("63a351045edffed66cfd");

const account = new Account(client);
const database = new Databases(client);
const storage = new Storage(client);

account.createAnonymousSession().then(
  (response) => {
    console.log(response);
  },
  (error) => {
    console.log(error);
  }
);
export default {
  name: "IndexPage",
  data() {
    return {
      podcasts: [],
      title: "",
    };
  },
  methods: {
    async getPodcasts() {
      const result = await storage.listFiles("63a4866686e5064b953d");

      let podcasts = [];

      result.files.map(async (file) => {
        let link = await storage.getFileView("63a4866686e5064b953d", file.$id);

        let podcastData = await database.listDocuments(
          "63a486f99305afe71e48",
          "63a487ea6bb5ba002a8c",
          [Query.equal("fileID", file.$id)]
        );

        podcasts.push({
          id: file.$id,
          link,
          title: podcastData.documents[0].title,
          date: new Date(podcastData.documents[0].date).toDateString(),
          name: podcastData.documents[0].name,
        });
      });

      this.podcasts = podcasts;
    },
    async uploadPodcast() {
      const file = await storage.createFile(
        "63a4866686e5064b953d",
        "unique()",
        document.getElementById("file").files[0]
      );

      await database.createDocument(
        "63a486f99305afe71e48",
        "63a487ea6bb5ba002a8c",
        "unique()",
        {
          fileID: file.$id,
          name: faker.name.fullName(),
          title: this.title,
          date: Date.now(),
        }
      );
    },
  },
  mounted() {
    this.getPodcasts();

    if (account.get !== null) {
      try {
        client.subscribe("documents", (response) => {
          this.getPodcasts();
        });
      } catch (error) {
        console.log(error, "error");
      }
    }
  },
};
</script>

<style>
.container {
  border: 3px solid #fff;
  padding: 20px;
}

input[type="text"] {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

input[type="file"] {
  width: 93%;
  background-color: #ffffff;
  color: rgb(0, 0, 0);
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button {
  background-color: #45a049;
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

.child {
  width: 45%;
  float: left;
  padding: 20px;
  border: 2px solid red;
  margin: 5px;
}
</style>
