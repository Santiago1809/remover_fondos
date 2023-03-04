
<script lang="ts">
  import { Cloudinary } from "@cloudinary/url-gen";
  import { backgroundRemoval } from "@cloudinary/url-gen/actions/effect";
  import Dropzone from "dropzone";
  import "dropzone/dist/dropzone.css";
  import { ImageStatus } from "../types.d";
  import { imageStatus, modifiedImage, originalImage } from "./store";

  import { onMount } from "svelte";

  const cloudinary = new Cloudinary({
    cloud: {
      cloudName: "detw4lwmv",
    },
    url: {
      secure: true,
    },
  });
  onMount(() => {
    const dropzone = new Dropzone("#dropzone", {
      uploadMultiple: false,
      acceptedFiles: ".jpg, .png, .webp",
      maxFiles: 1,
    });
    dropzone.on("sending", (file, xhr, formData) => {
      imageStatus.set(ImageStatus.UPLOADING);
      formData.append("upload_preset", "vsndnfvz");
      formData.append("timestamp", Date.now() / 1000);
      formData.append("api_key", 442929411282863);
    });

    
    dropzone.on("success", (file, response) => {
      imageStatus.set(ImageStatus.DONE);
      const { public_id: publicId, secure_url: url } = response;
      const imageWithoutBackground = cloudinary
        .image(publicId)
        .effect(backgroundRemoval());
      modifiedImage.set(imageWithoutBackground.toURL());
      originalImage.set(url);
    });
    dropzone.on("error", (file, response) => {
      console.log("Error");
      console.log(response);
    });
  });
</script>

<form
  id="dropzone"
  class="shadow-2xl border-gray-300 rounded-lg aspect-video w-full flex justify-center items-center flex-col"
  action="https://api.cloudinary.com/v1_1/detw4lwmv/image/upload"
>
  {#if $imageStatus === ImageStatus.READY}
    <button
      class=" font-bold pointer-events-none bg-blue-600 rounded-full text-bold text-white text-xl px-6 py-4"
    >
      Subida de archivos
    </button>
    <strong class="text-lg mt-4 text-gray-400">o arrastra un archivo</strong>
  {/if}

  {#if $imageStatus === ImageStatus.UPLOADING}
    <strong class="text-lg mt-4 text-gray-400">subiendo archivo</strong>
  {/if}
</form>
