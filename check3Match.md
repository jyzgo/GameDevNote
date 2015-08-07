bool MatchModel::checkMatch3Patterns()
{
    Cells &cells = MainScene::current()->getCells();
    
    
    
    //横向遍历，找到所有的匹配项
    vector< vector<Cell*> > horizontalVec;
    for (int curRow = MIN_ROW; curRow < MAX_ROWS; curRow++) {
        for (int curCol = MIN_COL;curCol < MAX_COLS; curCol ++)
        {
            //

            auto curCell = cells[curRow][curCol];
            
            if (curCell->isEmpty()) {
                continue;
            }
            
            int nextCol = curCol;
            vector<Cell*> matchCells;
            
            while (curRow < MAX_ROWS and nextCol < MAX_COLS and  cells[curRow][nextCol]->isBombable() and cells[curRow][nextCol]->color == curCell->color)
            {
                matchCells.push_back(cells[curRow][nextCol]);
                nextCol ++;
            }
            
            if (matchCells.size() >= 3) {
                //符合消除条件
                horizontalVec.push_back(matchCells);
                //更新坐标
                if (nextCol + 1 < MAX_COLS) {
                    curCol = nextCol + 1;
                }else
                {
                    curCol = 0;

                    curRow ++;
                    

                }
            }
            
        }
    }
    
    //纵向遍历，找到所有的匹配项
    vector< vector<Cell*> > verticalVec;
    for (int curCol = MIN_COL; curCol < MAX_COLS; curCol++) {
        for (int curRow = MIN_ROW;curRow < MAX_COLS; curRow ++)
        {
            //
            auto curCell = cells[curRow][curCol];
            int nextRow = curRow + 1 ;
            
            vector<Cell*> matchCells= {curCell};
            
            while (curCol < MAX_COLS and nextRow < MAX_ROWS and cells[nextRow][curCol]->color == curCell->color and cells[nextRow][curCol]->isBombable())
            {
                matchCells.push_back(cells[nextRow][curCol]);
                nextRow ++;
            }
            
            if (matchCells.size() >= 3) {
                //符合消除条件
                verticalVec.push_back(matchCells);
                //更新坐标
                if (nextRow + 1 < MAX_ROWS) {
                    curRow = nextRow + 1;
                }else
                {
                    curRow = 0;

                        curCol ++;
                    
                }
            }
            
        }
    }
    
    //合并前面两个生成的集合
    
    m_match3Vec.clear();
    
    m_match3Vec = horizontalVec;
    
    for (auto totalIt = m_match3Vec.begin(); totalIt != m_match3Vec.end(); ++totalIt)
    {
        for (auto verticalIt = verticalVec.begin(); verticalIt != verticalVec.end(); ++verticalIt)
        {
            if (mergeMatchPatten(*totalIt, *verticalIt)) {
                //移除
                verticalVec.erase(verticalIt);
                break;
                
            }
        }
    }
    
    //将剩余的没有被合并的vertical 合并进来
    
    for (int i = 0 ; i < verticalVec.size(); ++i)
    {
        m_match3Vec.push_back(verticalVec[i]);
    }
    
    if (m_match3Vec.size() > 0) {
        return true;
    }else
    {
        return false;
    }
}

void MatchModel::match3Patten()
{

    //开始消除
    
    startMatch3Elim();
    
    
    
}

void MatchModel::startMatch3Elim()
{
    if (m_match3Vec.size() == 0) {
        return;
    }

    resetMatch3ElimMark();
    
    auto main = MainScene::current();
    main->incEliminatorId();
    int elimId = main->getEliminatorId();
    
    for (int i = 0; i < m_match3Vec.size(); ++i)
    {
        auto vec = m_match3Vec[i];
        //首先，这里头有重复的cell，重复的就是所谓的节点
        
        elimAndGenBombMatch3(vec,elimId);
        
        
    }
    
    
    
}

void MatchModel::elimAndGenBombMatch3(const vector<Cell *> &vec,int elimId)
{
    auto main = MainScene::current();
    auto cells = main->getCells();
    vector<Cell*> toElimVec;
    vector<int>   toElimCount; //这里计算的是节点数，节点数最大的，形成炸弹
    for (int i = 0; i < vec.size(); ++i)
    {
        bool isExist = false;
        for (int j = 0; j < toElimVec.size(); ++j) {
            if (toElimVec[j] == vec[i]) {
                isExist = true;
                toElimCount[j]++;
            }
            
        }
        
        if (!isExist) {
            toElimVec.push_back(vec[i]);
            toElimCount.push_back(1);
        }
    }
    
    for (int i = 0 ; i <toElimVec.size(); ++i)
    {
        auto curCell = toElimVec[i];
        if (m_match3ElimMark[curCell->row][curCell->col] == 0) {
            m_match3ElimMark[curCell->row][curCell->col] = 1;
            int curElimid = elimId;
//            if (curCell->isBomb()) {
//                main-
//               curElimid = main->getEliminatorId();
//            }
            
            if (!curCell->isBombable()) {
                continue;
            }
            
            if (curCell->isBomb() && curCell->asBomb()->isColorBomb()) {
                curCell->asBomb()->skipFireState();
            }
            
            
            
            main->markCellEliminate(0.1, true, curCell, curElimid);
            
        }
        
    }
    
#ifdef __CHEAT
    if (Cheat::sharedCheat()->getIsMatchPlan1() ) {
        

        return;
    }
    
#endif

    if (toElimVec.size() == 4) {
        
        int off = toElimVec[1]->row - toElimVec[0]->row;
        Cell* curCell = toElimVec[1];
        auto bombT = BombType::eBombHorizonal;
        if (off == 0) {
            bombT = BombType::eBombVertical;
            
        }
        
        
        cells[curCell->row][curCell->col]->setReplaceByBomb(bombT);
        
    }else if (toElimVec.size() == 5 or toElimVec.size() == 6)
    {
        Cell* curCell = toElimVec[2];
        cells[curCell->row][curCell->col]->setReplaceByBomb(BombType::eBombSquare);
    }else if (toElimVec.size() > 6)
    {
        Cell* curCell = toElimVec[3];
        cells[curCell->row][curCell->col]->setReplaceByBomb(BombType::eBombColor);
    }
}

bool MatchModel::mergeMatchPatten(vector<Cell *> &final, vector<Cell *> &addVec)
{
    for (auto it = final.begin(); it != final.end(); ++it) {
        for (auto innerIt = addVec.begin() ; innerIt != addVec.end(); ++innerIt) {
            if (*it == *innerIt)//只要集合中有一个相交的,则将后面的vec加到前面去
            {
                final.insert(final.end(), addVec.begin(),addVec.end());
    
                return true;
            }
            
        }
    }
    return false;
}
