<template>
    <ion-page>
        <ion-header :translucent="true">
            <ion-toolbar>
                <ion-title>Home</ion-title>
                <ion-buttons slot="end">
                    <ion-button :router-link="{ name: 'EditNew' }">Add</ion-button>
                </ion-buttons>
            </ion-toolbar>
        </ion-header>

        <ion-content :fullscreen="true">
            <ion-header collapse="condense">
                <ion-toolbar>
                    <ion-title size="large">Home</ion-title>
                </ion-toolbar>
            </ion-header>
            <ion-item
                v-for="(note, noteIndex) of notes"
                :key="noteIndex"
                :router-link="{ name: 'EditExisting', params: { name: note.name } }"
            >
                {{ note.name }}
            </ion-item>
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
        IonItem,
        IonButtons,
        IonButton,
        onIonViewDidEnter
    } from '@ionic/vue';
    import { ref } from 'vue';
    import { Filesystem, Directory, FileInfo } from '@capacitor/filesystem';

    const notes = ref<FileInfo[]>([]);
    const noop  = () => true;

    const makeDir = async () => {
        return await Filesystem.mkdir({
            path: 'notes',
            directory: Directory.Documents
        });
    };

    const readDir = async () => {
        return await Filesystem.readdir({
            path: 'notes',
            directory: Directory.Documents
        });
    };

    const initFileSystem = () => {
        makeDir()
            .then(noop, noop)
                .then(readDir)
                    .then(({ files }) => {
                        notes.value = files.sort(
                            (a: FileInfo, b: FileInfo) =>
                                parseInt(b.name.replace('/note-/', '').replace('/.txt/', ''))
                                -
                                parseInt(a.name.replace('/note-/', '').replace('/.txt/', ''))
                        );
                    });
    };

    onIonViewDidEnter(async () => {
        await Filesystem.requestPermissions();
        initFileSystem();
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
