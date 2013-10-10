---                                                                                                                     
layout: page                                                                                                            
title:                                                                                   
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse                                                                                                       
footer: false                                                                                                           
---                                                                                                                     
## IR 2013 ([back](index.html))                                            
## Lecture 07. Lucene extended

```java
package ru.frostman.lucene;

import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.TextField;
import org.apache.lucene.index.*;
import org.apache.lucene.queryparser.classic.QueryParser;
import org.apache.lucene.search.*;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.RAMDirectory;
import org.apache.lucene.util.Version;
import org.apache.pdfbox.cos.COSDocument;
import org.apache.pdfbox.pdfparser.PDFParser;
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.util.PDFTextStripper;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

/**
 * @author slukjanov
 */
public class Main {
    private static final String[] files = new String[]{
            "/Users/frostman/Desktop/savanna-docs/savanna-0.1a1.pdf",
            "/Users/frostman/Desktop/savanna-docs/savanna-0.1a2.pdf",
            "/Users/frostman/Desktop/savanna-docs/savanna-0.1.pdf",
            "/Users/frostman/Desktop/savanna-docs/savanna-latest.pdf"
    };

    public static void main(String[] args) throws Exception {
        Directory directory = new RAMDirectory();
        Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_42);
        IndexWriterConfig iwc = new IndexWriterConfig(Version.LUCENE_42, analyzer);
        IndexWriter writer = new IndexWriter(directory, iwc);

        for (String file : files) {
            String text = parsePdf(file);
            Document doc = new Document();
            doc.add(new TextField("text", text, Field.Store.YES));
            doc.add(new TextField("file", file, Field.Store.YES));
            writer.addDocument(doc);
        }

        writer.commit();
        writer.close();

        IndexReader reader = DirectoryReader.open(directory);
        IndexSearcher searcher = new IndexSearcher(reader);

        System.out.println("Any");
        search(searcher, new MatchAllDocsQuery(), 10);

        QueryParser parser = new QueryParser(Version.LUCENE_42, "contents", analyzer);

        System.out.println("tox");
        search(searcher, parser.parse("text:tox"), 10);

        System.out.println("savanna");
        search(searcher, parser.parse("text:savanna"), 10);

        System.out.println("latest");
        search(searcher, parser.parse("text:latest"), 10);

        System.out.println("tox -e venv");
        PhraseQuery pq = new PhraseQuery();
        pq.setSlop(0);
        for (String word : "initialize database".split("\\s+")) {
            pq.add(new Term("text", word));
        }
        search(searcher, pq, 10);

    }

    private static void search(IndexSearcher searcher, Query q, int n) throws IOException {
        TopDocs topDocs = searcher.search(q, n);
        System.out.println(topDocs.totalHits);
        for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
            System.out.println(searcher.doc(scoreDoc.doc).getField("file").stringValue()
                    + " :: " + scoreDoc.score);
        }
    }

    private static String parsePdf(String file) throws IOException {
        FileInputStream fi = new FileInputStream(new File(file));
        PDFParser parser = new PDFParser(fi);
        parser.parse();
        COSDocument cd = parser.getDocument();
        PDFTextStripper stripper = new PDFTextStripper();
        return stripper.getText(new PDDocument(cd));
    }
}
```
