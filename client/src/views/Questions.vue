<template>
  <v-container grid-list-md>
    <v-dialog persistent v-model="dialogs.questions.new" max-width="700px">
      <v-card>
        <v-img
          class="white--text"
          src="@/assets/question_blue.png"
          position="top center"
          max-height="300"
        >
        </v-img>
        <v-card-text>
          <v-container fluid>
            <v-row>
              <v-col>
                <h1 style="color: black">New Question</h1>
              </v-col>
            </v-row>

            <v-row dense>
              <v-col>
                <v-text-field
                  v-model="newItem.n"
                  label="Name"
                  clearable
                  :error-messages="questionNameErrors"
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row dense>
              <v-col>
                <v-checkbox
                  v-model="newItem.m"
                  label="Measurable"
                  color="primary"
                ></v-checkbox>
              </v-col>

              <v-col>
                <v-checkbox
                  v-model="newItem.f"
                  label="Feedback"
                  color="primary"
                ></v-checkbox>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="closeDialog(dialogs.questions, 'new')"
            v-blur
            >Cancel</v-btn
          >
          <v-btn color="blue darken-1" text @click="newQuestionClose" v-blur
            >Save</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog persistent v-model="dialogs.values.new" max-width="700px">
      <v-card>
        <v-img
          class="white--text"
          src="@/assets/question_values_blue.png"
          position="top center"
          max-height="300"
        >
        </v-img>
        <v-card-text>
          <v-container fluid>
            <v-row dense>
              <v-col>
                <h1 style="color: black">New Value</h1>
              </v-col>
            </v-row>
            <v-row dense>
              <v-col>
                <v-subheader class="ma-0 pa-0">
                  To question:
                  {{ newItem.question != null ? newItem.question.n : "?" }}
                </v-subheader>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="2">
                <v-text-field
                  type="number"
                  min="1"
                  v-model="newItem.v"
                  label="Value"
                  @input="verifyValueValueInput"
                ></v-text-field>
              </v-col>
              <v-col>
                <v-text-field
                  v-model="newItem.d"
                  label="Description"
                  clearable
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="closeDialog(dialogs.values, 'new')"
            v-blur
            >Cancel</v-btn
          >
          <v-btn color="blue darken-1" text @click="newValueClose" v-blur
            >Save</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog persistent v-model="dialogs.questions.edit" max-width="700px">
      <v-card>
        <v-img
          class="white--text"
          src="@/assets/question_blue.png"
          position="top center"
          max-height="300"
        >
        </v-img>
        <v-card-text>
          <v-container fluid>
            <v-row>
              <v-col>
                <h1 style="color: black">Edit Question</h1>
              </v-col>
            </v-row>

            <v-row dense>
              <v-col>
                <v-text-field
                  v-model="editedItemCopy.n"
                  label="Name"
                  clearable
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row dense>
              <v-col>
                <v-tooltip bottom :open-on-hover="editedItemCopy.a">
                  <template v-slot:activator="{ on }">
                    <div v-on="on">
                      <v-checkbox
                        v-model="editedItemCopy.m"
                        label="Measurable"
                        color="primary"
                        :disabled="editedItemCopy.a"
                      ></v-checkbox>
                    </div>
                  </template>
                  <span
                    >Cannot change Question type if it has already been
                    answered</span
                  >
                </v-tooltip>
              </v-col>

              <v-col>
                <v-checkbox
                  v-model="editedItemCopy.f"
                  label="Feedback"
                  color="primary"
                ></v-checkbox>
              </v-col>

              <v-col>
                <v-checkbox
                  v-model="editedItemCopy.a"
                  label="Answered"
                  :disabled="true"
                  color="primary"
                ></v-checkbox>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="closeDialog(dialogs.questions, 'edit')"
            v-blur
            >Cancel</v-btn
          >
          <v-btn color="blue darken-1" text @click="editQuestionClose" v-blur
            >Save</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog persistent v-model="dialogs.values.edit" max-width="700px">
      <v-card>
        <v-img
          class="white--text"
          src="@/assets/question_values_blue.png"
          position="top center"
          max-height="300"
        >
        </v-img>
        <v-card-text>
          <v-container fluid>
            <v-row dense>
              <v-col>
                <h1 style="color: black">Edit Value</h1>
              </v-col>
            </v-row>
            <v-row dense>
              <v-col>
                <v-subheader class="ma-0 pa-0">
                  From question:
                  {{
                    editedItemCopy.question != null
                      ? editedItemCopy.question.n
                      : "?"
                  }}
                </v-subheader>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="2">
                <v-tooltip
                  bottom
                  :open-on-hover="
                    editedItemCopy.question != null && editedItemCopy.question.a
                  "
                >
                  <template v-slot:activator="{ on }">
                    <div v-on="on">
                      <v-text-field
                        type="number"
                        min="1"
                        v-model="editedItemCopy.v"
                        label="Value"
                        :disabled="
                          editedItemCopy.question != null &&
                            editedItemCopy.question.a
                        "
                      ></v-text-field>
                    </div>
                  </template>
                  <span
                    >Cannot change Value if question has already been
                    answered</span
                  >
                </v-tooltip>
              </v-col>
              <v-col>
                <v-text-field
                  v-model="editedItemCopy.d"
                  label="Description"
                  clearable
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="closeDialog(dialogs.values, 'edit')"
            v-blur
            >Cancel</v-btn
          >
          <v-btn color="blue darken-1" text @click="editValueClose" v-blur
            >Save</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog persistent v-model="dialogs.selected.delete" max-width="700px">
      <v-card
        ><v-card-title>
          <span class="headline"
            >Are you sure you want to delete
            <span class="red--text">{{ selectedItems.length }}</span>
            Question{{ selectedItems.length > 1 ? "s" : "" }}?</span
          >
        </v-card-title>
        <v-card-text>
          <span class="subtitle-1 red--text"
            >This action cannot be undone.</span
          >
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            v-blur
            @click="closeDialog(dialogs.selected, 'delete')"
          >
            CANCEL
          </v-btn>
          <v-btn text color="error" v-blur @click="deleteSelectedClose">
            Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog persistent v-model="dialogs.questions.delete" max-width="700px">
      <v-card>
        <v-card-text>
          <v-card-title>
            <span class="headline"
              >Are you sure you want to delete
              <span class="red--text">{{ deletingItem.name }}</span
              >?</span
            >
          </v-card-title>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            v-blur
            @click="closeDialog(dialogs.questions, 'delete')"
          >
            CANCEL
          </v-btn>
          <v-btn text color="error" v-blur @click="deleteQuestionClose">
            Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog persistent v-model="dialogs.values.delete" max-width="700px">
      <v-card>
        <v-card-text>
          <v-card-title>
            <span class="headline"
              >Are you sure you want to delete
              <span class="red--text"
                >{{ deletingItem.value }}. {{ deletingItem.description }}</span
              >?</span
            >
          </v-card-title>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            text
            color="primary"
            v-blur
            @click="closeDialog(dialogs.value, 'delete')"
          >
            CANCEL
          </v-btn>
          <v-btn text color="error" v-blur @click="deleteValueClose">
            Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-toolbar flat style="background-color: transparent">
      <v-toolbar-title>
        <h2>
          Questions
        </h2>
      </v-toolbar-title>
      <v-text-field
        hide-details
        prepend-icon="search"
        single-line
        class="ml-3"
        placeholder="Start typing to Search"
        v-model="filters.search"
        clearable
      ></v-text-field>
      <v-btn
        @click="newQuestionOpen"
        color="primary"
        class="ml-3 mb-2"
        v-on="on"
        v-blur
        >NEW QUESTION</v-btn
      >
    </v-toolbar>

    <v-container fluid dense>
      <v-row dense class="mt-n4">
        <v-col>
          <v-spacer></v-spacer>
        </v-col>
        <v-col cols="auto" :hidden="selectedItems.length === 0">
          <v-btn class="error" @click="deleteSelectedOpen"
            >DELETE SELECTED</v-btn
          >
        </v-col>
      </v-row>
      <v-row dense class="mb-n8">
        <v-col>
          <v-select
            dense
            chips
            label="Add Filters"
            multiple
            solo
            :items="filterChips"
            clearable
            deletable-chips
            @change="chipFilterAdded"
            menu-props="offsetY"
          >
          </v-select>
        </v-col>
      </v-row>
    </v-container>

    <v-data-table
      v-model="selectedItems"
      :headers="headers"
      :items="items"
      :search="filters.search"
      show-fist-last-page
      item-key="id"
      no-data-text="No Questions created so far."
      no-results-text="No Questions found."
      show-select
      show-expand
      must-sort
      class="elevation-1 ml-4 mr-4"
      :sort-by="['n']"
    >
      <template v-slot:header.data-table-expand="props">
        Values
      </template>

      <template v-slot:item.data-table-expand="{ item, isExpanded, expand }">
        <v-btn
          @click="expand(true)"
          icon
          v-blur
          v-if="!isExpanded && item.t !== 't'"
          ><v-icon>expand_more</v-icon></v-btn
        >
        <v-btn
          @click="expand(false)"
          icon
          v-blur
          v-if="isExpanded && item.t !== 't'"
          ><v-icon>expand_less</v-icon></v-btn
        >
      </template>

      <template v-slot:item.f="props">
        <v-checkbox
          primary
          hide-details
          :input-value="props.item.f"
          :disabled="true"
          class="mb-4"
          v-blur
        ></v-checkbox>
      </template>

      <template v-slot:item.m="props">
        <v-checkbox
          primary
          hide-details
          :input-value="props.item.t !== 't'"
          :disabled="true"
          v-blur
          class="mb-4"
        ></v-checkbox>
      </template>

      <template v-slot:item.a="props">
        <v-checkbox
          primary
          hide-details
          :input-value="props.item.a"
          :disabled="true"
          class="mb-4"
        ></v-checkbox>
      </template>

      <template v-slot:item.actions="props">
        <v-btn
          small
          class="ml-n1"
          icon
          v-blur
          @click="editQuestionOpen(props.item)"
        >
          <v-icon color="primary">
            edit
          </v-icon>
        </v-btn>

        <v-btn
          :disabled="props.item.a"
          small
          icon
          v-blur
          @click="deleteQuestionOpen(props.item)"
        >
          <v-icon color="error" v-on="on">
            delete
          </v-icon>
        </v-btn>
      </template>

      <template v-slot:expanded-item="props">
        <td :colspan="4 * headers.length" class="pa-0">
          <v-data-table
            :headers="questionValueHeaders"
            :items="props.item.v"
            :header-props="props.item"
            item-key="value"
            no-data-text="No Values created so far."
            must-sort
            disable-pagination
            :items-per-page="-1"
            class="elevation-0"
            hide-default-footer
            :sort-by="['v']"
          >
            <template v-slot:header.action_new="">
              <v-tooltip bottom :open-on-hover="props.item.a">
                <template v-slot:activator="{ on }">
                  <div v-on="on">
                    <v-btn
                      :disabled="props.item.a"
                      color="primary"
                      v-on="on"
                      v-blur
                      @click="newValueOpen(props.item)"
                      >NEW VALUE</v-btn
                    >
                  </div>
                </template>
                <span>You can only add new Values to unanswered Questions</span>
              </v-tooltip>
            </template>

            <template v-slot:item="{ item }">
              <tr>
                <td>{{ props.item.t === "c" ? item.v : "-" }}</td>
                <td>{{ item.d }}</td>
                <td>
                  <v-row class="ml-n1">
                    <v-btn
                      small
                      icon
                      v-blur
                      flat
                      @click="editValueOpen(item, props.item)"
                    >
                      <v-icon color="primary">
                        edit
                      </v-icon>
                    </v-btn>
                    <v-tooltip bottom :open-on-hover="props.item.a">
                      <template v-slot:activator="{ on }">
                        <div v-on="on">
                          <v-btn
                            :disabled="props.item.a"
                            small
                            icon
                            v-blur
                            flat
                            @click="deleteValueOpen(item, props.item)"
                          >
                            <v-icon color="error" v-on="on">
                              delete
                            </v-icon>
                          </v-btn>
                        </div>
                      </template>
                      <span
                        >You can only delete Values from unanswered
                        Questions</span
                      >
                    </v-tooltip>
                  </v-row>
                </td>

                <td></td>
              </tr>
            </template>
          </v-data-table>
        </td>
      </template>
    </v-data-table>

    <v-container fluid class="ml-4">
      <v-row>
        <h4 class="text--secondary mr-2">
          Note:
        </h4>
        <p class="text--secondary">
          Unanswered questions are fully editable. Answered questions only
          support specifying whether they are of type Feedback or not.
        </p>
      </v-row>
    </v-container>
  </v-container>
</template>

<script>
// import { required, integer, minLength, maxLength } from "vuelidate/lib/validators";

export default {
  data: () => ({
    snackbar: false,
    shown: true,
    items: [],
    filters: {
      search: "",
      measurable: undefined,
      feedback: undefined,
      answered: undefined
    },
    dialogs: {
      questions: {
        new: false,
        edit: false,
        delete: false
      },
      values: {
        new: false,
        edit: false,
        delete: false
      },
      selected: {
        delete: false
      }
    },
    newItem: {},
    editedItemCopy: {},
    deletingItem: {},
    defaultQuestion: {
      name: "",
      measurable: false,
      feedback: false,
      answered: false
    },
    defaultValue: {
      question: "",
      value: "",
      description: ""
    },
    selectedItems: [],
    filterChips: [
      {
        text: "Measurable",
        value: "m",
        disabled: false
      },
      {
        text: "Non measurable",
        value: "nm",
        disabled: false
      },
      {
        text: "Feedback",
        value: "f",
        disabled: false
      },
      {
        text: "Non feedback",
        value: "nf",
        disabled: false
      },
      {
        text: "Answered",
        value: "a",
        disabled: false
      },
      {
        text: "Not answered",
        value: "na",
        disabled: false
      }
    ]
  }),
  // validations: {
  //   [this.defaultValue.value]: { required, integer },
  //   [this.defaultValue.description]: { required, minLength: minLength(3), maxLength: maxLength(30) }
  // },
  async mounted() {
    this.items = await this.$store.state.dataStore.getQuestions();
  },
  computed: {
    questionValueHeaders() {
      return [
        { text: "Value", value: "v", width: "100", sortable: true },
        {
          text: "Description",
          value: "d",
          width: "auto",
          sortable: false
        },
        { text: "Actions", value: "action", width: "110", sortable: false },
        { text: "New", value: "action_new", width: "1", sortable: false }
      ];
    },
    headers() {
      return [
        {
          text: "Name",
          value: "n",
          sortable: true
        },
        {
          text: "Measurable",
          value: "m",
          sortable: false,
          width: "1",
          filter: value => {
            if (this.filters.measurable == null) return true;
            return value === this.filters.measurable;
          }
        },
        {
          text: "Feedback",
          value: "f",
          sortable: false,
          width: "1",
          filter: value => {
            if (this.filters.feedback == null) return true;
            return value === this.filters.feedback;
          }
        },
        {
          text: "Answered",
          value: "a",
          sortable: false,
          width: "1",
          filter: value => {
            if (this.filters.answered == null) return true;
            return value === this.filters.answered;
          }
        },
        { text: "Actions", value: "actions", width: "110", sortable: false }
      ];
    },

    questionNameErrors() {
      const errors = [];
      // if (!newItem)
      // if (!this.defaultValue.value.$dirty) return errors;
      // !this.defaultValue.value.required && errors.push("Este campo es obligatorio");
      return errors;
    }
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    deleteDialog(val) {
      val || this.close();
    }
  },
  methods: {
    newQuestionOpen() {
      this.newItem = Object.assign({}, this.defaultQuestion);
      this.openDialog(this.dialogs.questions, "new");
    },
    newValueOpen(question) {
      this.newItem = Object.assign({}, this.defaultValue);
      this.newItem.question = question;
      this.openDialog(this.dialogs.values, "new");
    },
    newQuestionClose() {
      // Save to DB
      this.closeDialog(this.dialogs.questions, "new");
    },
    newValueClose() {
      // Save to DB
      this.closeDialog(this.dialogs.values, "new");
    },

    editQuestionOpen(item) {
      this.editedItemCopy = Object.assign({}, item);
      this.openDialog(this.dialogs.questions, "edit");
    },
    editValueOpen(item, question) {
      this.editedItemCopy = Object.assign({}, item);
      this.editedItemCopy.question = question;
      this.openDialog(this.dialogs.values, "edit");
    },
    editQuestionClose() {
      // Save to DB
      this.closeDialog(this.dialogs.questions, "edit");
    },
    editValueClose() {
      // Save to DB
      this.closeDialog(this.dialogs.values, "edit");
    },

    deleteSelectedOpen() {
      this.openDialog(this.dialogs.selected, "delete");
    },
    deleteQuestionOpen(item) {
      this.deletingItem = item;
      this.openDialog(this.dialogs.questions, "delete");
    },
    deleteValueOpen(item, question) {
      this.deletingItem = item;
      this.deletingItem.question = question;
      this.openDialog(this.dialogs.values, "delete");
    },
    deleteSelectedClose() {
      this.selectedItems = [];
      this.closeDialog(this.dialogs.selected, "delete");
    },
    deleteQuestionClose() {
      // Save to DB
      this.closeDialog(this.dialogs.questions, "delete");
    },
    deleteValueClose() {
      // Save to DB
      this.closeDialog(this.dialogs.values, "delete");
    },

    chipFilterAdded(items) {
      var otherWasSelected = false;
      if (items.indexOf("m") !== -1) {
        if (!this.filterChips[1].disabled) this.filterChips[1].disabled = true;
        if (!this.filters.measurable) this.filters.measurable = true;
        otherWasSelected = true;
      } else {
        if (this.filterChips[1].disabled) this.filterChips[1].disabled = false;
      }
      if (items.indexOf("nm") !== -1) {
        if (!this.filterChips[0].disabled) this.filterChips[0].disabled = true;
        if (this.filters.measurable !== false) this.filters.measurable = false;
      } else {
        if (this.filterChips[0].disabled) this.filterChips[0].disabled = false;
        if (!otherWasSelected && this.filters.measurable !== undefined)
          this.filters.measurable = undefined;
      }

      otherWasSelected = false;
      if (items.indexOf("f") !== -1) {
        if (!this.filterChips[3].disabled) this.filterChips[3].disabled = true;
        if (!this.filters.feedback) this.filters.feedback = true;
        otherWasSelected = true;
      } else {
        if (this.filterChips[3].disabled) this.filterChips[3].disabled = false;
      }
      if (items.indexOf("nf") !== -1) {
        if (!this.filterChips[2].disabled) this.filterChips[2].disabled = true;
        if (this.filters.feedback !== false) this.filters.feedback = false;
      } else {
        if (this.filterChips[2].disabled) this.filterChips[2].disabled = false;
        if (!otherWasSelected && this.filters.feedback !== undefined)
          this.filters.feedback = undefined;
      }

      otherWasSelected = false;
      if (items.indexOf("a") !== -1) {
        if (!this.filterChips[5].disabled) this.filterChips[5].disabled = true;
        if (!this.filters.answered) this.filters.answered = true;
        otherWasSelected = true;
      } else {
        if (this.filterChips[5].disabled) this.filterChips[5].disabled = false;
      }
      if (items.indexOf("na") !== -1) {
        if (!this.filterChips[4].disabled) this.filterChips[4].disabled = true;
        if (this.filters.answered !== false) this.filters.answered = false;
      } else {
        if (this.filterChips[4].disabled) this.filterChips[4].disabled = false;
        if (!otherWasSelected && this.filters.answered !== undefined)
          this.filters.answered = undefined;
      }
    },

    openDialog(item, type) {
      if (item == null || type == null || item[type] == null) return;
      if (!item[type]) item[type] = true;
    },
    closeDialog(item, type) {
      if (item == null || type == null || item[type] == null) return;
      if (item[type]) item[type] = false;
    }
  }
};
</script>
