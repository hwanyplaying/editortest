<template>
  <div
    style="width: 500px; overflow: hidden; height: 390px"
    ref="scrollContainer"
  >
    <div>
      <table style="height: 100%; table-layout: fixed">
        <colgroup>
          <col style="width: 10%" />
          <col style="width: 25%" />
          <col style="width: 25%" />
          <col style="width: 25%" />
          <col style="width: 15%" />
        </colgroup>
        <thead style="height: 100%; background: blue">
          <tr>
            <th>No</th>
            <th>이름</th>
            <th>IP</th>
            <th>호스트 그룹</th>
            <th>잔여 VD</th>
          </tr>
        </thead>
      </table>
      <div
        style="max-height: 305px; overflow-y: scroll; margin-bottom: 1.2rem"
        @scroll="checkIntersection"
      >
        <table style="width: 100%; table-layout: fixed">
          <colgroup>
            <col style="width: 10%" />
            <col style="width: 25%" />
            <col style="width: 25%" />
            <col style="width: 25%" />
            <col style="width: 15%" />
          </colgroup>
          <tbody ref="tableBody">
            <tr v-for="(item, index) in itemList" :key="index">
              <td style="text-align: center">{{ item.idx }}</td>
              <td style="text-align: center">{{ item.name }}</td>
              <td style="text-align: center">{{ item.ip }}</td>
              <td style="text-align: center">{{ item.hostGroup }}</td>
              <td style="text-align: center">{{ item.remainingVD }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "editorTest",
  data() {
    return {
      itemList: [], // 데이터를 저장할 배열
      currentPage: 1, // 현재 페이지 번호
      pageSize: 10, // 한 페이지에 표시할 아이템 수
    };
  },
  methods: {
    // IntersectionObserver를 사용하여 스크롤 이벤트를 감지
    checkIntersection() {
      const container = this.$refs.scrollContainer;
      const tbody = this.$refs.tableBody;
      const lastRow = tbody.lastElementChild;

      const observer = new IntersectionObserver(
        (entries) => {
          if (entries[0].isIntersecting) {
            // 마지막 <tr>이 화면에 나타날 때 데이터를 가져옴
            this.loadMoreData();
            observer.unobserve(lastRow);
          }
        },
        { root: container }
      );

      observer.observe(lastRow);
    },

    // 더미 데이터를 생성하여 itemList에 추가
    loadMoreData() {
      // 여기에 더미 데이터를 생성하는 로직을 추가
      const newData = this.generateDummyData(this.pageSize);
      this.itemList = [...this.itemList, ...newData];
      this.currentPage++;
    },
    // 더미 데이터 생성 함수
    generateDummyData(count) {
      const data = [];
      for (let i = 0; i < count; i++) {
        data.push({
          idx: (this.currentPage - 1) * this.pageSize + i + 1,
          name: "테스트",
          ip: "172.16.5.115",
          hostGroup: "Host1",
          remainingVD: 30,
        });
      }
      return data;
    },
  },
  mounted() {
    // 초기로딩 시 데이터 가져오기
    this.loadMoreData();
    this.loadMoreData();
  },
};
</script>

<style module></style>
