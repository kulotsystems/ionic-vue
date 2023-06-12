<template>
    <ion-page>
        <ion-header :translucent="true">
            <ion-toolbar>
                <ion-title>Edit</ion-title>
                <ion-buttons slot="end">
                    <ion-button @click="save">Save</ion-button>
                </ion-buttons>
            </ion-toolbar>
        </ion-header>

        <ion-content :fullscreen="true">
            <quill-editor
                theme="snow"
                v-model:content="content"
                content-type="html"
                :toolbar="[
                    [{ header: [1, 2, 3, 4, 5, 6, false] }],
                    ['bold', 'italic', 'underline', 'strike'],
                    [{ list: 'ordered' }, { list: 'bullet' }],
                    [{ indent: '-1'}, { indent: '+1' }]
                ]"
            />
        </ion-content>
    </ion-page>
</template>


<script setup lang="ts">
    import {
        IonContent,
        IonHeader,
        IonPage,
        IonTitle,
        IonToolbar,
        IonButtons,
        IonButton, onIonViewDidEnter
    } from '@ionic/vue';
    import { ref } from 'vue';
    import { useRoute, useRouter } from 'vue-router';
    import { Filesystem, Directory, Encoding } from '@capacitor/filesystem';
    import { QuillEditor } from '@vueup/vue-quill';
    import '@vueup/vue-quill/dist/vue-quill.snow.css';

    const route   = useRoute();
    const router  = useRouter();
    const content = ref('');

    const save = async () => {
        const file = route.params.name || `note-${Date.now()}.txt`;
        await Filesystem.writeFile({
            path: `notes/${file}`,
            data: content.value || '',
            directory: Directory.Documents,
            encoding: Encoding.UTF8
        });
        router.back();
    };

    onIonViewDidEnter(async () => {
        if(route.params.name) {
            const fileContent = await Filesystem.readFile({
                path: `notes/${route.params.name}`,
                directory: Directory.Documents
            });

            content.value = fileContent.data;
        }
    });
</script>


<style scoped>
    #container {
        text-align: center;

        position: absolute;
        left: 0;
        right: 0;
        top: 50%;
        transform: translateY(-50%);
    }

    #container strong {
        font-size: 20px;
        line-height: 26px;
    }

    #container p {
        font-size: 16px;
        line-height: 22px;
        color: #8c8c8c;
        margin: 0;
    }

    #container a {
        text-decoration: none;
    }
</style>
