<script>
import axios from 'axios';
import AppLoader from './AppLoader.vue';
import ProjectCard from './ProjectCard.vue';
import { store } from '../store';

export default {
    name: "AppMain",
    components: { ProjectCard, AppLoader },
    data() {
        return {
            store,
            projects: [],
            currentPage: 1,
            lastPage: null,
            totalProjects: null,
            loading: false
        };
    },
    created() {
        this.getProjects(1);
    },
    methods: {
        getProjects(page) {
            const options = {
                params: {
                    page
                }
            }
            this.loading = true;
            axios.get(`${this.store.apiBaseUrl}/api/projects`, options).then(resp => {
                this.projects = resp.data.results.data;
                this.currentPage = resp.data.results.current_page;
                this.lastPage = resp.data.results.last_page;
                this.totalPosts = resp.data.results.total;
                this.loading = false;
            });
        }
    },
}
</script>

<template>
    <div class="container">
        <h2 class="text-center mb-3">Tutti i progetti</h2>
        <AppLoader v-if="loading" />
        <div v-else class="row justify-content-center py-4">
            <div class="col-11 col-md-10 col-lg-8">
                <p class="text-end">Totale {{ totalProjects }} progetti trovati</p>
                <ProjectCard :project="project" v-for="project in projects" :key="project.id" />
                <nav class="navigation d-flex justify-content-between">
                    <div>
                        Pagina {{ currentPage }} di {{ lastPage }}
                    </div>
                    <div>
                        <a class="btn btn-success me-2" :class="currentPage === 1 ? 'disabled' : ''" href=""
                            @click.prevent="getPosts(currentPage - 1)">Indietro</a>
                        <a class="btn btn-success" :class="{ 'disabled': currentPage === lastPage }" href=""
                            @click.prevent="getPosts(currentPage + 1)">Avanti</a>
                    </div>
                </nav>
            </div>
        </div>
    </div>
</template>

<style lang="scss">

</style>