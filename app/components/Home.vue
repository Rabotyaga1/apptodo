<template>
    <Page class="page">
        <ActionBar>
            <Label text="TODO"/>
        </ActionBar>

        <FlexboxLayout flexDirection="column" alignItems="center" justifyContent="flex-start">
            <Button class="button" :text="buttonTextStr"  @tap="buttonText()" />
            <FlexboxLayout flexDirection="column" v-if="activator">
                <TextField hint="Введите название задачи" v-model="task.title" />
                <TextField hint="Введите задачу" v-model="task.body" />
                <Button class="button" text="Создать задачу"  @tap="saveTask()" />
            </FlexboxLayout>
            <Label v-if="!taskList[0]" text="Здесь пусто =("></Label>
            <FlexboxLayout v-else flexDirection="column" alignItems="center" justifyContent="flex-start">
                <FlexboxLayout v-for="(tasks, index) in taskList" :key="index" justifyContent="space-between" class="card" @tap="completeTask(index)"> 
                    <FlexboxLayout flexDirection="column" justifyContent="center" alignItems="flex-start"> 
                        <Label class="card__title" :text="tasks.title"></Label>
                        <Label class="card__body" :text="tasks.body"></Label>
                    </FlexboxLayout>
                    <Label v-if="tasks.status" text="Выполнено"></Label>
                    <Label v-if="!tasks.status" text="Не выполнено"></Label>
                    <Button text="Удалить" @tap="removeTask(index)"/>
                </FlexboxLayout>
            </FlexboxLayout>
        </FlexboxLayout>
    </Page>
</template>

<script>
    import { ApplicationSettings } from "@nativescript/core";

    export default {
        computed: {

        },
        data(){
            return{
                activator: false,
                buttonTextStr: "Добавить",
                task:{
                    title: null,
                    body: null,
                    status: false
                },
                taskList: [],
                gettedTasks: [],
            }
        },
        methods:{
            saveTask(){
                this.taskList.unshift(this.task);
                let strTask = JSON.stringify(this.taskList)

                ApplicationSettings.setString('todo', strTask);
                this.activator = false;
                
                this.recieveTasks();

                this.task.title = null;
                this.task.body = null;
                this.task.status = false;
            },
            recieveTasks(){
                this.taskList = JSON.parse(ApplicationSettings.getString('todo'))
                console.log(this.taskList)
            },
            removeTask(index){
                this.taskList.splice(index, 1);
                ApplicationSettings.setString('todo', JSON.stringify(this.taskList));
                this.recieveTasks();
            },
            completeTask(index){
                this.taskList[index].status = true
                ApplicationSettings.setString('todo', JSON.stringify(this.taskList));
            },
            buttonText(){
                this.activator = !this.activator
                if(!this.activator){
                    this.buttonTextStr = "Добавить"
                }
                else{
                    this.buttonTextStr = "Скрыть"
                }
            }
        },
        mounted(){
            this.recieveTasks()
        }
    };
</script>

<style scoped lang="scss">
    @import '@nativescript/theme/scss/variables/blue';

    // Custom styles
    .card{
        width: 90%;
        height: 200px;
        border-radius: 15px;
        padding-left: 20px;
        box-shadow: 9px 6px 18px 0px rgba(156, 158, 159, 0.6);
    }
    .card__title{
        font-size: 15px;
        font-weight: 600;
    }
    .card__body{
        font-size: 13px;
    }
    .page{
        background-color: #f7f8fc;
    }
    .button{
        width: 90%;
    }
</style>
