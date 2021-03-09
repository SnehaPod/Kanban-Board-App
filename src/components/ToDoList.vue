<template>
  <div class="todo-list">
    <navbar-component></navbar-component>
    <div class="container mt-4">
      <div class="row">
        <!-- Added Tasks -->
        <div class="col-lg-3 col-md-6 col-sm-12">
          <div class="task-type text-left mb-3">Tasks</div>

          <!-- List of tasks -->
          <draggable class="list-group" v-model="todosTasks" animation: 200 handle=".task-handle1" :group="{name:'tasks', pull: true, put: true}" @start="drag=true" @end="drag=false" @change="log('Tasks', $event)">
            <!-- <div > -->
              <b-card
                v-for="(todo) in todosTasks" :key="todo.id"
                text-variant="dark"
                class="text-left todo-card-title px-0 mb-2 cursor-pointer"
                v-bind:style="{ 'background-color': todo.bgColor }"
              >
                <b-card-text class="d-flex">
                  <div class="task-handle1" v-b-tooltip.hover title="Drag to re-order">
                    <b-icon
                      class="icons border-secondary mr-1 text-muted"
                      scale="0.85"
                      icon="list"
                    ></b-icon>
                  </div>
                  <div @click="showDescModal(todo.id)">
                    {{ todo.title }} 
                  </div>

                  <div>
                    <b-icon
                      class="icons border-secondary ml-1 text-muted"
                      scale="0.85"
                      icon="trash"
                      @click="deleteCard(todo.id)"
                    ></b-icon>
                  </div>
                </b-card-text>
              </b-card>
            <!-- </div> -->
          </draggable>
          <!-- -->

           <!-- Modals for Card Description -->
          <div v-for="todo in todosTasks" :key="`desc-${todo.id}`">
                <b-modal
                  :id="`cardDescription-${todo.id}`"
                  centered
                  title="Title"
                  hide-footer=true
                  @ok="addCardDescription(todo)"
                >
                  <template #modal-header="{ ok, close }" class="modal-header-desc">
                    <span class="todo-card-header">
                      {{ todo.title }}
                    </span>

                    <!-- Header save & close buttons  -->
                    <div class="d-flex">
                      <span @click="ok()">
                        <b-icon
                          class="icons border-secondary mx-3"
                          variant="secondary"
                          scale="1.25"
                          icon="check-circle"
                        ></b-icon>
                      </span>

                      <span @click="close()">
                        <b-icon
                          class="icons border-secondary"
                          variant="secondary"
                          scale="1.25"
                          icon="x-circle"
                        ></b-icon>
                      </span>
                    </div>
                    <!--  -->
                  </template>

                  <div>
                    <div class="desc-title">Description</div>
                    <b-form-textarea
                      class="newTaskDesc"
                      type="text-area"
                      placeholder="Enter a description for this task"
                      v-model="todo.description"
                    ></b-form-textarea>
                  </div>

                  <!-- Color picker in Modal -->
                  <div class="color-picker mt-2">
                    <p class="my-1">Color</p>
                    <b-form-group v-slot="{ ariaDescribedby }">
                      <b-form-radio-group
                        id="color-picker"
                        v-model="todo.bgColor"
                        :aria-describedby="ariaDescribedby"
                        buttons
                        name="radio-sub-component"
                      >
                        <b-form-radio
                          v-for="(bgcolor, colorIndex) in bgColors"
                          :value="bgcolor.value"
                          :key="colorIndex"
                          class="color-display"
                          v-bind:style="{
                            'background-color': bgcolor.value,
                            height: '25px',
                            width: '25px',
                          }"
                        ></b-form-radio>
                      </b-form-radio-group>
                    </b-form-group>
                  </div>
                  <!--  -->
                </b-modal>
          </div>
          <!--  -->

          <!-- Add new card -->
          <b-card
            no-body
            text-variant="dark"
            class="text-left todo-card-title px-0 mb-2 add-new-task py-0"
            style="background: none"
          >
            <div
              v-if="!isTaskAddNewCardHidden"
              class="px-2 py-5 text-center cursor-pointer"
              @click="isTaskAddNewCardHidden = true"
            >
              + Add New Card
            </div>

            <div v-if="isTaskAddNewCardHidden">
              <b-form-textarea
                class="newTaskDesc border-0 text-muted"
                id="newTaskDesc"
                type="text-area"
                no-resize
                
                placeholder="Enter a title for this card..."
                v-model="newTodo.title"
              ></b-form-textarea>

              <div>
                <b-button-group>
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 border-0 bg-transparent font-weight-bold addCard-buttons"
                    @click="addNewCard('Tasks')"
                    >Add Card</b-button
                  >
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 text-muted border-0 bg-transparent font-weight-light addCard-buttons"
                    @click="isTaskAddNewCardHidden = !isTaskAddNewCardHidden"
                    >Cancel</b-button
                  >
                </b-button-group>
              </div>
            </div>
          </b-card>
          <!--  -->
        </div>
        <!--  -->

        <!-- Completed Tasks -->
        <div class="col-lg-3 col-md-6 col-sm-12">
          <div class="task-type text-left mb-3">Completed</div>

          <!-- List of tasks -->
          <draggable class="list-group" v-model="todosCompleted" animation: 200 handle=".task-handle2" :group="{name:'tasks', pull: true, put: true}" @start="drag=true" @end="drag=false" @change="log('Completed', $event)" >
            <!-- <div > -->
                    <b-card
                      v-for="(todo) in todosCompleted" :key="todo.id"
                      text-variant="dark"
                      class="text-left todo-card-title px-0 mb-2 cursor-pointer"
                      v-bind:style="{ 'background-color': todo.bgColor }"
                    >
                      <b-card-text class="d-flex">
                        <div class="task-handle2" v-b-tooltip.hover title="Drag to re-order">
                          <b-icon
                            class="icons border-secondary mr-1 text-muted"
                            scale="0.85"
                            icon="list"
                          ></b-icon>
                        </div>
                        <div @click="showDescModal(todo.id)">
                          {{ todo.title }} 
                        </div>

                        <div>
                          <b-icon
                            class="icons border-secondary ml-1 text-muted"
                            scale="0.85"
                            icon="trash"
                            @click="deleteCard(todo.id)"
                          ></b-icon>
                        </div>
                      </b-card-text>
                    </b-card>
            <!-- </div> -->
          </draggable>
          <!-- -->

          <!-- Modals for Card Description -->
          <div v-for="todo in todosCompleted" :key="`desc-${todo.id}`">
            <b-modal
              :id="`cardDescription-${todo.id}`"
              centered
              title="Title"
              hide-footer=true
              @ok="addCardDescription(todo)"
            >
              <template #modal-header="{ ok, close }" class="modal-header-desc">
                <span class="todo-card-header">
                  {{ todo.title }}
                </span>

                <!-- Header save & close buttons  -->
                <div class="d-flex">
                  <span @click="ok()">
                    <b-icon
                      class="icons border-secondary mx-3"
                      variant="secondary"
                      scale="1.25"
                      icon="check-circle"
                    ></b-icon>
                  </span>

                  <span @click="close()">
                    <b-icon
                      class="icons border-secondary"
                      variant="secondary"
                      scale="1.25"
                      icon="x-circle"
                    ></b-icon>
                  </span>
                </div>
                <!--  -->
              </template>

              <div>
                <div class="desc-title">Description</div>
                <b-form-textarea
                  class="newTaskDesc"
                  type="text-area"
                  placeholder="Enter a description for this task"
                  v-model="todo.description"
                ></b-form-textarea>
              </div>

              <!-- Color picker in Modal -->
              <div class="color-picker mt-2">
                <p class="my-1">Color</p>
                <b-form-group v-slot="{ ariaDescribedby }">
                  <b-form-radio-group
                    id="color-picker"
                    v-model="todo.bgColor"
                    :aria-describedby="ariaDescribedby"
                    buttons
                    name="radio-sub-component"
                  >
                    <b-form-radio
                      v-for="(bgcolor, colorIndex) in bgColors"
                      :value="bgcolor.value"
                      :key="colorIndex"
                      class="color-display"
                      v-bind:style="{
                        'background-color': bgcolor.value,
                        height: '25px',
                        width: '25px',
                      }"
                    ></b-form-radio>
                  </b-form-radio-group>
                </b-form-group>
              </div>
              <!--  -->
            </b-modal>
          </div>
          <!--  -->

          <!-- Add new card -->
          <b-card
            no-body
            text-variant="dark"
            class="text-left todo-card-title px-0 mb-2 add-new-task py-0"
            no-resize
            
            style="background: none"
          >
            <div
              v-if="!isCompletedAddNewCardHidden"
              class="px-2 py-5 text-center cursor-pointer"
              @click="isCompletedAddNewCardHidden = true"
            >
              + Add New Card
            </div>

            <div v-if="isCompletedAddNewCardHidden">
              <b-form-textarea
                class="newTaskDesc border-0 text-muted"
                id="newTaskDesc"
                type="text-area"
                no-resize
                placeholder="Enter a title for this card..."
                
                v-model="newTodo.title"
              ></b-form-textarea>

              <div>
                <b-button-group>
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 border-0 bg-transparent font-weight-bold addCard-buttons"
                    @click="addNewCard('Completed')"
                    >Add Card</b-button
                  >
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 text-muted border-0 bg-transparent font-weight-light addCard-buttons"
                    @click="
                      isCompletedAddNewCardHidden = !isCompletedAddNewCardHidden
                    "
                    >Cancel</b-button
                  >
                </b-button-group>
              </div>
            </div>
          </b-card>
          <!--  -->
        </div>
        <!--  -->

        <!-- Deferred Tasks -->
        <div class="col-lg-3 col-md-6 col-sm-12">
          <div class="task-type text-left mb-3">Deferred</div>

          <!-- List of tasks -->
          <draggable class="list-group" v-model="todosDeferred" handle=".task-handle3" :group="{name:'tasks', pull: true, put: true}" @start="drag=true" @end="drag=false" @change="log('Deferred', $event)" >
            <!-- <div > -->
                    <b-card
                      v-for="(todo) in todosDeferred" :key="todo.id"
                      text-variant="dark"
                      class="text-left todo-card-title px-0 mb-2 cursor-pointer"
                      v-bind:style="{ 'background-color': todo.bgColor }"
                    >
                      <b-card-text class="d-flex">
                        <div class="task-handle3" v-b-tooltip.hover title="Drag to re-order">
                          <b-icon
                            class="icons border-secondary mr-1 text-muted"
                            scale="0.85"
                            icon="list"
                          ></b-icon>
                        </div>
                        <div @click="showDescModal(todo.id)">
                          {{ todo.title }} 
                        </div>

                        <div>
                          <b-icon
                            class="icons border-secondary ml-1 text-muted"
                            scale="0.85"
                            icon="trash"
                            @click="deleteCard(todo.id)"
                          ></b-icon>
                        </div>
                      </b-card-text>
                    </b-card>
            <!-- </div> -->
          </draggable>
          <!--  -->

          <!-- Modal for Card Description -->
          <div v-for="todo in todosDeferred" :key="`desc-${todo.id}`">
            <b-modal
              :id="`cardDescription-${todo.id}`"
              centered
              title="Title"
              hide-footer=true
              @ok="addCardDescription(todo)"
            >
              <template #modal-header="{ ok, close }" class="modal-header-desc">
                <span class="todo-card-header">
                  {{ todo.title }}
                </span>

                <!-- Header save & close buttons  -->
                <div class="d-flex">
                  <span @click="ok()">
                    <b-icon
                      class="icons border-secondary mx-3"
                      variant="secondary"
                      scale="1.25"
                      icon="check-circle"
                    ></b-icon>
                  </span>

                  <span @click="close()">
                    <b-icon
                      class="icons border-secondary"
                      variant="secondary"
                      scale="1.25"
                      icon="x-circle"
                    ></b-icon>
                  </span>
                </div>
                <!--  -->
              </template>

              <div>
                <div class="desc-title">Description</div>
                <b-form-textarea
                  class="newTaskDesc"
                  type="text-area"
                  placeholder="Enter a description for this task"
                  v-model="todo.description"
                ></b-form-textarea>
              </div>

              <!-- Color picker in Modal -->
              <div class="color-picker mt-2">
                <p class="my-1">Color</p>
                <b-form-group v-slot="{ ariaDescribedby }">
                  <b-form-radio-group
                    id="color-picker"
                    v-model="todo.bgColor"
                    :aria-describedby="ariaDescribedby"
                    buttons
                    name="radio-sub-component"
                  >
                    <b-form-radio
                      v-for="(bgcolor, colorIndex) in bgColors"
                      :value="bgcolor.value"
                      :key="colorIndex"
                      class="color-display"
                      v-bind:style="{
                        'background-color': bgcolor.value,
                        height: '25px',
                        width: '25px',
                      }"
                    ></b-form-radio>
                  </b-form-radio-group>
                </b-form-group>
              </div>
              <!--  -->
            </b-modal>
          </div>
          <!--  -->

          <!-- Add new card -->
          <b-card
            no-body
            text-variant="dark"
            class="text-left todo-card-title px-0 mb-2 add-new-task py-0"
            style="background: none"
            no-resize
            
          >
            <div
              v-if="!isDeferredAddNewCardHidden"
              class="px-2 py-5 text-center cursor-pointer"
              @click="isDeferredAddNewCardHidden = true"
            >
              + Add New Card
            </div>

            <div v-if="isDeferredAddNewCardHidden">
              <b-form-textarea
                class="newTaskDesc border-0 text-muted"
                id="newTaskDesc"
                type="text-area"
                no-resize
                placeholder="Enter a title for this card..."
                
                v-model="newTodo.title"
              ></b-form-textarea>

              <div>
                <b-button-group>
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 border-0 bg-transparent font-weight-bold addCard-buttons"
                    @click="addNewCard('Deferred')"
                    >Add Card</b-button
                  >
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 text-muted border-0 bg-transparent font-weight-light addCard-buttons"
                    @click="
                      isDeferredAddNewCardHidden = !isDeferredAddNewCardHidden
                    "
                    >Cancel</b-button
                  >
                </b-button-group>
              </div>
            </div>
          </b-card>
          <!--  -->

        </div>
        <!--  -->

        <!-- Rejected Tasks -->
        <div class="col-lg-3 col-md-6 col-sm-12">
          <div class="task-type text-left mb-3">Rejected</div>

          <!-- List of tasks -->
          <draggable class="list-group" v-model="todosRejected" handle=".task-handle4" :group="{name:'tasks', pull: true, put: true}" @start="drag=true" @end="drag=false" @change="log('Rejected', $event)" >
            <!-- <div > -->
                    <b-card
                      v-for="(todo) in todosRejected" :key="todo.id"
                      text-variant="dark"
                      class="text-left todo-card-title px-0 mb-2 cursor-pointer"
                      v-bind:style="{ 'background-color': todo.bgColor }"
                    >
                      <b-card-text class="d-flex">
                        <div class="task-handle4" v-b-tooltip.hover title="Drag to re-order">
                          <b-icon
                            class="icons border-secondary mr-1 text-muted"
                            scale="0.85"
                            icon="list"
                          ></b-icon>
                        </div>
                        <div @click="showDescModal(todo.id)">
                          {{ todo.title }} 
                        </div>

                        <div>
                          <b-icon
                            class="icons border-secondary ml-1 text-muted"
                            scale="0.85"
                            icon="trash"
                            @click="deleteCard(todo.id)"
                          ></b-icon>
                        </div>
                      </b-card-text>
                    </b-card>
            <!-- </div> -->
          </draggable>
          <!--  -->

          <!-- Modal for Card Description -->
          <div v-for="todo in todosRejected" :key="`desc-${todo.id}`">
            <b-modal
              :id="`cardDescription-${todo.id}`"
              centered
              title="Title"
              hide-footer=true
              @ok="addCardDescription(todo)"
            >
              <template #modal-header="{ ok, close }" class="modal-header-desc">
                <span class="todo-card-header">
                  {{ todo.title }}
                </span>

                <!-- Header save & close buttons  -->
                <div class="d-flex">
                  <span @click="ok()">
                    <b-icon
                      class="icons border-secondary mx-3"
                      variant="secondary"
                      scale="1.25"
                      icon="check-circle"
                    ></b-icon>
                  </span>

                  <span @click="close()">
                    <b-icon
                      class="icons border-secondary"
                      variant="secondary"
                      scale="1.25"
                      icon="x-circle"
                    ></b-icon>
                  </span>
                </div>
                <!--  -->
              </template>

              <div>
                <div class="desc-title">Description</div>
                <b-form-textarea
                  class="newTaskDesc"
                  type="text-area"
                  placeholder="Enter a description for this task"
                  v-model="todo.description"
                ></b-form-textarea>
              </div>

              <!-- Color picker in Modal -->
              <div class="color-picker mt-2">
                <p class="my-1">Color</p>
                <b-form-group v-slot="{ ariaDescribedby }">
                  <b-form-radio-group
                    id="color-picker"
                    v-model="todo.bgColor"
                    :aria-describedby="ariaDescribedby"
                    buttons
                    name="radio-sub-component"
                  >
                    <b-form-radio
                      v-for="(bgcolor, colorIndex) in bgColors"
                      :value="bgcolor.value"
                      :key="colorIndex"
                      class="color-display"
                      v-bind:style="{
                        'background-color': bgcolor.value,
                        height: '25px',
                        width: '25px',
                      }"
                    ></b-form-radio>
                  </b-form-radio-group>
                </b-form-group>
              </div>
              <!--  -->
            </b-modal>
          </div>
          <!--  -->

          <!-- Add new card -->
          <b-card
            no-body
            text-variant="dark"
            class="text-left todo-card-title px-0 mb-2 add-new-task py-0"
            style="background: none"
            no-resize
            
          >
            <div
              v-if="!isRejectedAddNewCardHidden"
              class="px-2 py-5 text-center cursor-pointer"
              @click="isRejectedAddNewCardHidden = true"
            >
              + Add New Card
            </div>

            <div v-if="isRejectedAddNewCardHidden">
              <b-form-textarea
                class="newTaskDesc border-0 text-muted"
                id="newTaskDesc"
                type="text-area"
                no-resize
                placeholder="Enter a title for this card..."
                
                v-model="newTodo.title"
              ></b-form-textarea>

              <div>
                <b-button-group>
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 border-0 bg-transparent font-weight-bold addCard-buttons"
                    @click="addNewCard('Rejected')"
                    >Add Card</b-button
                  >
                  <b-button
                    variant="light"
                    class="mt-4 mb-2 text-muted border-0 bg-transparent font-weight-light addCard-buttons"
                    @click="
                      isRejectedAddNewCardHidden = !isRejectedAddNewCardHidden
                    "
                    >Cancel</b-button
                  >
                </b-button-group>
              </div>
            </div>
          </b-card>
          <!--  -->
        </div>
        <!--  -->

      </div>
    </div>
  </div>
</template>

<script>
import NavbarComponent from "./partials/Navbar";
import draggable from "vuedraggable";
export default {
  name: "ToDoList",
  data() {
    return {
      todosTasks: [],
      todosCompleted: [],
      todosDeferred: [],
      todosRejected: [],
      todosArray: localStorage.getItem("todosArray")
        ? JSON.parse(localStorage.getItem("todosArray"))
        : [],
      isTaskAddNewCardHidden: false,
      isCompletedAddNewCardHidden: false,
      isDeferredAddNewCardHidden: false,
      isRejectedAddNewCardHidden: false,
      bgColors: [
        { value: "rgb(254,200,216,0.4)" }, // pink
        { value: "rgb(224,187,228,0.4)" }, // lavender
        { value: "rgb(210,233,218,0.4)" }, // light mint
        { value: "rgb(249,202,200,0.4)" }, // salmon
        { value: "rgb(201,214,223,0.4)" }, // baby blue
        { value: "rgb(247,238,207,0.4)" }, // off-white
      ],
      newTodo: {
        id: "",
        title: "",
        description: "",
        status: "",
        bgColor: "",
        order: "",
      },
    };
  },
  mounted() {
    this.populateTodos();
    this.sortTodos();
    this.isTaskAddNewCardHidden = false;
    this.isCompletedAddNewCardHidden = false;
    this.isDeferredAddNewCardHidden = false;
    this.isRejectedAddNewCardHidden = false;
  },
  methods: {
    // Segregate todos based on status
    populateTodos: function () {
      this.todosTasks = this.todosArray.filter((el) => el.status == "Tasks");
      this.todosCompleted = this.todosArray.filter(
        (el) => el.status == "Completed"
      );
      this.todosDeferred = this.todosArray.filter(
        (el) => el.status == "Deferred"
      );
      this.todosRejected = this.todosArray.filter(
        (el) => el.status == "Rejected"
      );
    },
    // Sort todos based on order
    sortTodos: function () {
      this.todosTasks = this.todosTasks.sort((a, b) => {
        if (a.order < b.order) {
          return -1;
        }
        if (a.order > b.order) {
          return 1;
        }
        return 0;
      });
      this.todosCompleted = this.todosCompleted.sort((a, b) => {
        if (a.order < b.order) {
          return -1;
        }
        if (a.order > b.order) {
          return 1;
        }
        return 0;
      });
      this.todosDeferred = this.todosDeferred.sort((a, b) => {
        if (a.order < b.order) {
          return -1;
        }
        if (a.order > b.order) {
          return 1;
        }
        return 0;
      });
      this.todosRejected = this.todosRejected.sort((a, b) => {
        if (a.order < b.order) {
          return -1;
        }
        if (a.order > b.order) {
          return 1;
        }
        return 0;
      });
    },
    // Reset props
    reset: function () {
      this.isTaskAddNewCardHidden = false;
      this.isCompletedAddNewCardHidden = false;
      this.isDeferredAddNewCardHidden = false;
      this.isRejectedAddNewCardHidden = false;
      this.newTodo = {
        title: "",
        status: "",
        description: "",
        bgColor: "",
        order: "",
      };
    },
    // Generate random bgColor for each newly added card
    randomizeBackground: function () {
      return this.bgColors[Math.floor(Math.random() * this.bgColors.length)][
        "value"
      ];
    },
    // Add a new card
    addNewCard: function (type) {
      this.newTodo.status = type;
      this.newTodo.id = this.getLastId() + 1;
      this.newTodo.order = this.getOrderSeq(type) + 1;
      this.todosArray = localStorage.getItem("todosArray")
        ? JSON.parse(localStorage.getItem("todosArray"))
        : [];
      this.todosArray.push(this.newTodo);
      this.newTodo.bgColor = this.randomizeBackground();
      if (this.newTodo.title !== "") {
        localStorage.setItem("todosArray", JSON.stringify(this.todosArray));
        this.reset();
        this.populateTodos();
      }
    },
    // Add description for the card from the modal
    addCardDescription: function (todo) {
      this.todosArray = localStorage.getItem("todosArray")
        ? JSON.parse(localStorage.getItem("todosArray"))
        : [];
      if (this.todosArray.length) {
        this.todosArray.forEach(function (el, index) {
          if (this[index].id == todo.id) {
            this[index] = todo;
          }
        }, this.todosArray);

        localStorage.setItem("todosArray", JSON.stringify(this.todosArray));
        this.reset();
        this.populateTodos();
      }
    },
    // Delete a card
    deleteCard: function (todoId) {
      if (confirm("Do you really want to delete the card?")) {
        var index = this.todosArray.findIndex((el) => {
          return el.id == todoId;
        });
        this.todosArray.splice(index, 1);
        localStorage.setItem("todosArray", JSON.stringify(this.todosArray));
        this.reset();
        this.populateTodos();
      }
    },
    // Open the description modal
    showDescModal: function (todoId) {
      var modalId = "cardDescription" + "-" + todoId;
      this.$bvModal.show(modalId);
    },
    // Get counter for autoincrementing id
    getLastId: function () {
      this.todosArray = this.todosArray.sort((a, b) =>
        a.id > b.id ? -1 : b.id > a.id ? 1 : 0
      );
      return this.todosArray[0] && this.todosArray[0].id
        ? this.todosArray[0].id
        : 0;
    },
    // Get counter for setting the order for item
    getOrderSeq: function (status) {
      var arrName = "";
      switch (status) {
        case "Tasks":
          arrName = "todosTasks";
          break;
        case "Completed":
          arrName = "todosCompleted";
          break;
        case "Deferred":
          arrName = "todosDeferred";
          break;
        case "Rejected":
          arrName = "todosRejected";
          break;
      }

      this[arrName] = this[arrName].sort((a, b) =>
        a.order > b.order ? -1 : b.order > a.order ? 1 : 0
      );
      return this[arrName][0] && this[arrName][0].order
        ? this[arrName][0].order
        : 0;
    },
    // Log changes for draggable elements
    log: function (statusType, event) {
      if (event.moved) {
        var status = event.moved.element.status;
        var arrName;
        let self = this;
        switch (status) {
          case "Tasks":
            arrName = "todosTasks";
            break;
          case "Completed":
            arrName = "todosCompleted";
            break;
          case "Deferred":
            arrName = "todosDeferred";
            break;
          case "Rejected":
            arrName = "todosRejected";
            break;
        }

        this[arrName].map((todo, index) => {
          todo.order = index + 1;
        });

        this[arrName].forEach((todo) => {
          self.todosArray.forEach(function (el, index) {
            if (this[index].id == todo.id) {
              this[index] = todo;
            }
          }, self.todosArray);

          localStorage.setItem("todosArray", JSON.stringify(this.todosArray));
        });
      }
      if (event.added) {
        this.todosArray.forEach(function (el, index) {
          if (this[index].id == event.added.element.id) {
            this[index].status = statusType;
            this[index].order = event.added.newIndex + 1;
          }
        }, this.todosArray);
        localStorage.setItem("todosArray", JSON.stringify(this.todosArray));
      }
    },
  },
  components: {
    "navbar-component": NavbarComponent,
    draggable,
  },
};
</script>

<style scoped>
.task-type {
  font-family: "Quicksand", sans-serif;
  font-size: 20px;
  font-weight: 600;
}

.todo-card-title {
  font-family: "Quicksand", sans-serif;
  font-weight: 600;
  font-size: 13px;
  border: none;
  border-radius: 6px;
  padding: 3px 3px 3px 3px;
}

.color-display {
  display: inline-block;
  width: 30px;
  height: 30px;
  font-size: 8px;
  margin: 0 auto;
  margin-right: 7px;
  border: 1px solid rgb(172, 141, 141);
  border-radius: 7px !important;
}

.color-display.active {
  background: url("../assets/check.svg") no-repeat center;
}

.add-new-task {
  border: 1px dashed rgb(172 184 206);
}

.todo-card-header {
  font-family: "Quicksand", sans-serif;
  font-weight: 600;
  font-size: 16px;
}

.desc-title {
  font-family: "Quicksand", sans-serif;
  font-weight: 600;
  font-size: 13px;
}

.icons {
  cursor: pointer;
}

.cursor-pointer {
  cursor: pointer;
}

.newTaskDesc {
  font-size: 14px;
}

.addCard-buttons {
  font-size: 15px;
}

.addCard-buttons:focus,
textarea:focus {
  outline: 0px !important;
  -webkit-appearance: none;
  box-shadow: none !important;
}

.modal-header-desc {
  border-bottom: 0px !important;
}

.flip-list-move {
  transition: transform 0.5s;
}
</style>