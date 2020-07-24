# NAVIGATE-THE-MARS-ROVER
 THE MARS COLONIZATION PROGRAM
 #include<iostream>
#include<vector>
#include<string>
#include<list>
using namespace std;    

//class to store edges possible route......

 class Edge{
     public:
        int v=0;
        int w=0;
        Edge(int v,int w){
            this->v=v;
            this->w=w;
        }
        Edge(){
        }
    };


//to store edges of each vertex...........
vector<vector<Edge *>>graph;

//adding edges.............
void addEdge(int u,int v,int w){
        if(u<0 || v<0 || u>=graph.size() || v>=graph.size())
        return;
        graph[u].push_back(new Edge(v,w));
        graph[v].push_back(new Edge(u,w)); //to make edges bidirectional edge is added to vector list of both u and v.
    }


//display graph.............
//will represent whole grid system 
void display(){
    for(int i=0;i<graph.size();i++){
        cout<<i<<" => ";
        for(int j=0;j<graph[i].size();j++){
       cout<<"("<< graph[i][j]->v<< ","<<graph[i][j]->w<<")";
        } 
        cout<<endl; 
    }
} 
    

//remove edge..................
//this can be useful when some road are not opend or diverted ,may be due to construction work or natural calamity
    void removeEdge(int u,int v){
        int i=-1;
        int j=-1;
        for(int k=0;k<graph[u].size();k++){
            if(graph[u][k]->v==v){
                i=k;
                break;
            }
        }
         for(int k=0;k<graph[v].size();k++){
            if(graph[v][k]->v==u){
                j=k;
                break;
            }
        }

        graph[u].erase(graph[u].begin()+i);
        graph[v].erase(graph[v].begin()+j);
    }


//remove vertex................
//this can be useful if due to certain circumstance one station is closed for some time
    void removeVertex(int u){
        for(int i=graph[u].size()-1;i>=0;i--){
            removeEdge(graph[u][i]->v,u);
        }
    }

//haspath(1 random path)................
//this will give result whether there is path between given src and destination ie 
//in railway aap we can directly now if there is no direct train between given source and destination
    bool hasPath(int src,int des,vector<bool>& visited,string ans){
        if(src==des){
            cout<<ans<<endl;
            return true;
        }
        visited[src]=true;
        bool res=false;
        for(Edge *e:graph[src]){
            int v=e->v;
            if(!visited[v]){

                res=res||hasPath(v,des,visited,ans+to_string(v)+"->");
            }
        }
        return res;
    }


//all possible paths......................
//this can be neede when shortest path is not only the aim ,our choice also depend on other factors too
     void allPath(int src,int des,vector<bool>& visited,string ans){
        if(visited[src]) return;
        if(src==des){
            cout<<ans<<endl;
            return;
        }
        visited[src]=true;
        for(Edge *e:graph[src]){
            int v=e->v;
            allPath(v,des,visited,ans+to_string(v)+"->");

        }
        visited[src]=false;
    }    
    
//to print weights of all paths..................
//if there is not significant difference then we can also prefer otherone 
  void Pathnweight(int src,int w,vector<bool>& visited,string ans){
        
        visited[src]=true;

        cout<<to_string(src)+ " -> "+ans +to_string(src)+" @ "+ to_string(w)<<endl;
        for(Edge *e:graph[src]){
            int v=e->v;
            int wt=e->w;
            if(!visited[v])
            Pathnweight(v,w+wt,visited,ans+to_string(src));

        }
        visited[src]=false;
    }

// to get lightest weight method 1(in source to dest).............. 
//weight here is equal to the distance between two points 
//this class is needed to store possible string of paths 
class Pair_path{
    public:
    int wt=100000;
    string s=" ";
    Pair_path(int wt,string s){
        this->wt=wt;
        this->s=s;

    }
    Pair_path(){

    }
};
Pair_path * lightestweight(int src,int des,vector<bool>&visited){
    if(src==des){
        Pair_path* obj=new Pair_path(0,to_string(src)+" ");
        return obj;
    }
    visited[src]=true;
    Pair_path* myans=new Pair_path();
    for(Edge *e:graph[src]){
        int nbr=e->v;
        int w=e->w;
        if(!visited[nbr]){
            Pair_path *recans=lightestweight(nbr,des,visited);
            if(recans->wt+w< myans->wt){
                myans->wt=recans->wt+w;
                myans->s=to_string(src)+" "+recans->s;
            }
            }
    }
    visited[src]=false;
    return myans;

}


//to get lightest weight method 2 in source to destination....................
    int fwt=100000;
    string fans="";
    void lightestweight_01(int src,int des,vector<bool>& visited,int wt,string ans){
        if(src==des){
            if(wt<fwt){
                fwt=wt;
                fans=ans;
            }
            return;
        }

        visited[src]=true;
        for(Edge *e:graph[src]){
            int nbr=e->v;
            int w=e->w;
            if(!visited[nbr]){
                lightestweight_01(nbr,des,visited,w+wt,ans+to_string(nbr));
            }
        }
        visited[src]=false;
    }



//solve......................
void solve(){
    //this for loop only when we are not giving size on top !by this we are giving vector graph the initial 7 values that r nothing else but 7 different vector lists.
    for(int i=0;i<8;i++){
        vector<Edge*>ar;
        graph.push_back(ar);
    }
        addEdge(0,1,10);
        addEdge(0,3,10);
        addEdge(1,2,10);
        addEdge(2,3,40);
        addEdge(3,4,2);
        addEdge(4,5,2);
        addEdge(5,6,8);
        addEdge(4,6,3);
       
        vector<bool>visited(graph.size(),false);
         display();
        hasPath(0,6,visited,to_string(0)+"->");
        allPath(0,6,visited,to_string(0)+"->");
        
      
        Pathnweight(0,0,visited," ") ;
  
         Pair_path* ans=lightestweight(0,6,visited);
         cout<<ans->s<<" -> "<<ans->wt<<endl; 
        
        int src=0;
        lightestweight_01(src,5,visited,0,to_string(src));   
        cout<<fans<< " @ "<<fwt<<endl;  

        
}

//main.......................................................
 int main(){
        solve();
        return 0;
    }

