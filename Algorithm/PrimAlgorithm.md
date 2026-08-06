# 최소 신장 트리(MST; Minimum spanning tree) 알고리즘

- 그래프의 모든 정점을 가장 적은 비용으로 연결하되 사이클이 생기지 않도록 하는 최소 신장 트리를 찾는 방법
- 사이클을 방지하기 위해 정점 v개일 때 v-1개이 간선이 선택됨 

-  가장 대표적인 알고리즘으로 Kruskal Algorithm, Prim Algorithm이 있다.


# Prim Algorithm

방법
1. 임의의 정점 선택
2. 새로 포함된 정점과 연결된 간선 중 가장 작은 가중치를 갖는 간선 선택
   1. 사이클이 형성되면 버림
   2. 사이클이 형성되지 않으면 새로운 정점을 추가
3. 모든 정점을 포함할 때까지 반복

자료구조
가장 작은 가중치를 갖는 간선을 선택하기 위해 *우선순위 큐* 사용


## 구현

구현 예시 - [문제](https://github.com/dee021/CodingTest/commit/8f3f04f9cd655fe11256fff89276c58092494689)
```java
// 우선순위를 지정하기 위해 가중치를 저장하는 Edge 클래스
class Edge implements Comparable<Edge> {
	int v1, v2;
	long dist;
	
	Edge (int v1, int v2, long[] spot1, long[] spot2) {
		this.v1 = v1;
		this.v2 = v2;
		long dx = Math.abs(spot1[0] - spot2[0]), dy = Math.abs(spot1[1] - spot2[1]);
		this.dist = dx*dx + dy*dy;
	}
	
	public long getDist() {return this.dist;}
	public int[] getVertex() {return new int[] {this.v1, this.v2};}

	@Override
	public int compareTo(Edge o) {
		// TODO Auto-generated method stub
		return Long.compare(this.dist, o.getDist());
	}

	@Override
	public String toString() {
		return "Edge [v1=" + v1 + ", v2=" + v2 + ", dist=" + dist + "]";
	}
}

public class Main {
    // 정점의 위치를 저장하는 배열
	static long[][] island = new long[1000][2];

    // 구한 간선을 저장하는 우선순위 큐
	static PriorityQueue<Edge> pq = new PriorityQueue<>();
	
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st;
        StringBuilder sb = new StringBuilder();
        
        int n = Integer.parseInt(br.readLine());
        for (int i = 0; i < 2; i++) {
        	st = new StringTokenizer(br.readLine(), " ");
        	for (int j = 0; j < n; j++) island[j][i] = Integer.parseInt(st.nextToken());
        }
        long res = tunnel(n);
    	  double e = Double.parseDouble(br.readLine());
        
        System.out.println(String.format("#%d %d\n", t, (long)Math.round(res * e)));
        
        br.close();
    }
    
    static long tunnel(int n) {
    	boolean visited[] = new boolean[n];
    	visited[0] = true;
    	
    	addEdge(n, 0, visited);
    	
    	long totalDist = 0;
    	
    	for (int i = 0; i < n-1; i++) { // round
    		Edge e = pq.poll();
    		while (visited[e.getVertex()[0]] && visited[e.getVertex()[1]]) {
				// 이미 포함된 정점이므로 버림
				e = pq.poll();
			}
    		if (!visited[e.getVertex()[0]]) {
				visited[e.getVertex()[0]] = true;
				addEdge(n, e.getVertex()[0], visited);
			} else if (!visited[e.getVertex()[1]]) {
				visited[e.getVertex()[1]] = true;
				addEdge(n, e.getVertex()[1], visited);
			} else {
				System.out.println("error");
			}
			
			totalDist += e.getDist();
		}
    	return totalDist;
    }
    
    static void addEdge(int n, int newV, boolean[] visited) {
    	for (int i = 0; i < n; i++) {
    		if (visited[i]) continue; // 사이클을 방지하기 위해 이미 포함된 정점 넘기기
    		pq.add(new Edge(i, newV, island[i], island[newV]));
    	}
    }
}
```
