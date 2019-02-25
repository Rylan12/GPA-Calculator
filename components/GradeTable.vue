<template>
  <div>
    <el-alert
      title="Enter a letter grade or percentage"
      type="error"
      :closable="false"
      show-icon
      v-if="!valid">
    </el-alert>
    <el-row :gutter="20">
      <div v-for="(row, index) in tableData">
        <el-col :xs="11" :sm="9" :md="9" :lg="9" :xl="9">
          <el-form>
            <el-form-item>
              <el-input placeholder="Class name" v-model="row.name"></el-input>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :xs="13" :sm="6" :md="6" :lg="6" :xl="6">
          <el-form>
            <el-form-item>
              <el-select v-model="row.level" filterable placeholder="Level">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :xs="11" :sm="4" :md="4" :lg="4" :xl="4">
          <el-form :model="row" :rules="rules1" ref="form">
            <el-form-item prop="grades">
              <el-input class="grade-input" placeholder="Semester 1 Grade" v-model="row.grades[0]"></el-input>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :xs="11" :sm="4" :md="4" :lg="4" :xl="4">
          <el-form :model="row" :rules="rules2" ref="form">
            <el-form-item prop="grades">
              <el-input class="grade-input" placeholder="Semester 2 Grade" v-model="row.grades[1]"></el-input>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :xs="2" :sm="1" :md="1" :lg="1" :xl="1">
          <div class="remove-button-wrapper">
            <el-button type="danger" icon="el-icon-minus" circle size="mini" @click="remove(index)"
                       :disabled="tableData.length === 1"></el-button>
          </div>
        </el-col>
      </div>
    </el-row>
    <div class="add">
      <el-button class="add-button" type="primary" @click="add">Add Class <i class="el-icon-plus el-icon-right"></i>
      </el-button>
    </div>
  </div>
</template>

<script>
// Get the number of points to subtract from 4/4.5/5 based on grade
function getPointValue(grade, level) {
  let loss = 0;
  // Set level to core if not set
  if (level === '') {
    level = 4
  }
  // Remove % sign if present
  grade = grade.replace('%', '');
  // Convert grade to number if possible
  grade = isNaN(Number(grade)) ? grade : Number(grade);
  if (typeof grade === "string") {
    grade = grade.toUpperCase();
    switch (grade) {
      case "A":
      case "a":
        loss = 0;
        break;
      case "B":
      case "b":
        loss = 1;
        break;
      case "C":
      case "c":
        loss = 2;
        break;
      case "D":
      case "d":
        loss = 3;
        break;
      case "F":
      case "f":
        return 0;
      default:
        return -1;
    }
  } else if (typeof grade === "number") {
    if (grade >= 89.5) {
      loss = 0;
    } else if (grade >= 79.5) {
      loss = 1;
    } else if (grade >= 69.5) {
      loss = 2;
    } else if (grade >= 59.5) {
      loss = 3;
    } else {
      return 0;
    }
  }
  return level - loss;
}

export default {
  data() {
    function validator(rule, value, cb) {
      // console.log(value);
      // run cb with no parameters for no error
      if (value.length === 0 || /^([ABCDF]|((\.\d+)|(\d+\.?\d*)%?))$/i.test(value)) cb();
      // run cb with an error to show an error
      cb(new Error(" "));
    }

    return {
      tableData: [{
        name: '',
        level: '',
        grades: ['', '']
      }],
      options: [{
        value: 4,
        label: 'Core'
      }, {
        value: 4.5,
        label: 'Honors'
      }, {
        value: 5,
        label: 'Advanced'
      }],
      rules1: {grades: [{validator: (rule, value, cb) => validator(rule, value[0], cb), trigger: 'blur'}]},
      rules2: {grades: [{validator: (rule, value, cb) => validator(rule, value[1], cb), trigger: 'blur'}]},
      valid: true,
    }
  },
  methods: {
    handleEdit(index, row) {
      // console.log(index, row);
    },
    handleDelete(index, row) {
      // console.log(index, row);
    },
    add() {
      this.tableData.push({
        name: '',
        level: '',
        grades: ['', '']
      });
    },
    remove(index) {
      this.tableData.splice(index, 1);
    },
  },
  mounted() {
    this.$emit("change", this.gpa);
  },
  computed: {
    gpa() {
      let weightedPoints = 0;
      let unweightedPoints = 0;
      let classes = 0;

      // Cycle through each class
      this.tableData.forEach(element => {
        // Divide all point values in half if PE class
        let multiplier = 1;
        if (element.name === 'PE') {
          multiplier = 0.5;
        }
        // Cycle through each semester
        element.grades.forEach(grade => {
          // Continue if grade is blank
          if (grade === "") {
            return;
          }
          // Add class and point value
          let points = [getPointValue(grade, element.level) * multiplier, getPointValue(grade, 4) * multiplier];
          if (points[0] !== -1) {
            classes += multiplier;
            weightedPoints += getPointValue(grade, element.level) * multiplier;
          }
          if (points[1] !== -1) {
            unweightedPoints += getPointValue(grade, 4) * multiplier;
          }
        });
      });

      // Make sure classes are entered (to avoid dividing by 0)
      if (classes !== 0) {
        let gpa = [
          (Math.round(weightedPoints / classes * 1000) / 1000),
          (Math.round(unweightedPoints / classes * 1000) / 1000)
        ];
        // console.log(gpa);
        return gpa
      }

      // Return empty if no classes
      // console.log("None");
      return [null, null];
    },
  },
  watch: {
    gpa(gpa) {
      this.$emit("change", gpa);
    },
    tableData: {
      handler() {
        const data = [];
        const dataCount = this.$refs.form.length;
        this.$refs.form.forEach(form => {
          form.validate(valid => {
            data.push(valid);
            if (data.length === dataCount) {
              this.valid = !data.some(ele => !ele);
            }
          })
        })
      },
      deep: true
    },
  },
}
</script>

<style scoped lang="scss">
$row-height: 50px;

.add-button {
  margin-right: 12px;
}

.add {
  float: right;
  margin-top: 12px;
  margin-bottom: 12px;
}

.el-icon-right {
  padding-left: 12px;
}

.el-form-item {
  margin: 0;
}

.el-select {
  width: 100%;
}

.el-row * {
  margin-bottom: 5px;
}

.el-alert {
  margin-bottom: 1em;
}

.el-form-item.is-success /deep/ .el-input__inner {
  border: 1px solid #dcdfe6;

  &:hover {
    border: 1px solid #c0c4cc;
  }
}

.remove-button-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}

.el-col {
  height: $row-height;
}
</style>
